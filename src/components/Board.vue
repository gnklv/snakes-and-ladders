<template>
  <div class="l-container">

    <div class="board__centroid">

      <div class="board" id="playBoard">

        <div class="board__row" v-for="row in rowArray">

          <div class="board__cell" 
            v-for="item in row" 
            :data-numb="item.id"
            :class="{ 
              'board__cell--good': item.state === stateGood,
              'board__cell--good-end': item.state === stateGoodEnd,
              'board__cell--bad': item.state === stateBad,
              'board__cell--bad-end': item.state === stateBadEnd
            }"
          >
            <span class="board__numb">
              {{ item.id + 1 }}
            </span>
          </div>
          

        </div>
        <canvas id="waysCanvas" class="board__arrow"></canvas>
        <canvas id="playerCanvas" class="board__arrow"></canvas>

      </div>

      <dice 
        :dice="steps" 
        :diceCount="diceCount" 
        @dice-result="initDice"
      ></dice>

    </div>


  </div>
</template>

<script>
import Dice from '@/components/Dice'

export default {
  name: 'board',
  props: {
    boardSize: {
      type: Number
    },
    playerCount: {
      type: Number
    },
    diceCount: {
      type: Number
    }
  },
  data () {
    return {
      gameFile: [],
      ceillPairs: [],
      stateGood: 'good',
      stateGoodEnd: 'good-end',
      stateBad: 'bad',
      stateBadEnd: 'bad-end',
      rand: null,
      randEnd: null,
      lineCoords: {
        start: [],
        fin: []
      },
      steps: null,
      flag: false,
      user: []
    }
  },
  methods: {
    init () {
      this.buildBoard();

      for (let i = 0; i < this.boardSize*1.5; i++) {
        this.setState(this.stateGood, 1, this.emptyCells.length - 2, 'start', i);
        this.setState(this.stateGoodEnd, this.rand + 1, this.emptyCells.length - 2, 'fin', i);

        this.setState(this.stateBad, 1, this.emptyCells.length - 2, 'start', i);
        this.setState(this.stateBadEnd, 0, this.rand - 1, 'fin', i);
      }
    },
    buildBoard () {
      for( let i = 0; i < Math.pow(this.boardSize, 2); i++ ) {
        this.gameFile.push({ 
          id: i, 
          state: 'default',
          stateId: null,
          player: false,
          empty: true,
          row: Math.floor(i / this.boardSize),
          order: i - (this.boardSize * Math.floor(i / this.boardSize))
        });
      }
    },
    setState(state, min, max, pos, i) {
      this.rand = _.random(min, max);
      let cellObj = this.emptyCells[this.rand];

      if (cellObj) {
        cellObj.state = state;
        cellObj.stateId = i;
        cellObj.empty = false;

        this.lineCoords[pos].push({
          x: this.getCellPosition(cellObj.id).x,
          y: this.getCellPosition(cellObj.id).y,
          id: cellObj.id
        });
      } else {
        this.setState(state, min, max, pos, i);
      }
    },
    getCellPosition (id, r, o) {
      let row, order, x, y;

      if (id) {
        row = this.gameFile[id].row;
        order = this.gameFile[id].order;
      } else {
        row = r;
        order = o
      }

      x = order * 50 + 30;
      y = row * 50 + 30;
      //console.log(`row: ${row}, order: ${order}, x: ${x}, y: ${y}, id: ${id}`);
      return { x, y };
    },
    createWays() {
      const canvas = document.getElementById('waysCanvas'),
            board = document.getElementById('playBoard'),
            ctx = canvas.getContext('2d');

      canvas.width = board.offsetWidth;
      canvas.height = board.offsetHeight;

      for (let i = 0; i < this.lineCoords.start.length; i++) {
        ctx.beginPath();
        ctx.moveTo(this.lineCoords.start[i].x, this.lineCoords.start[i].y);
        ctx.lineTo(this.lineCoords.fin[i].x, this.lineCoords.fin[i].y);
        ctx.lineWidth = 2;
        if ( i % 2 === 0 ) {
          ctx.strokeStyle = '#f1c40f';
        } else {
          ctx.strokeStyle = '#e74c3c';
        }
        ctx.stroke();
        ctx.closePath();
      }
    },
    canvasPlayer() {
      /*const canvas = document.getElementById('playerCanvas'),
            board = document.getElementById('playBoard'),
            ctx = canvas.getContext('2d'),
            width = board.offsetWidth,
            height = board.offsetHeight;

      canvas.width = width;
      canvas.height = height;

      let x = this.user[0].pos[0].x,
          y = this.user[0].pos[0].y,
          r = 15,
          duration = 500,
          nextX = this.user[0].pos[1].x,
          nextY = this.user[0].pos[1].y,
          startTime;

      var anim = (time) => {
        if (!startTime) { startTime = time || performance.now() };

        let deltaTime = (time - startTime) / duration,
            currentX = x + ((nextX - x) * deltaTime),
            currentY = y + ((nextY - y) * deltaTime);

        if (deltaTime >= 1) {
          x = nextX;
          y = nextY;
          startTime = null;
          draw(x, y);
        } else {
          draw(currentX, currentY);
          requestAnimationFrame(anim);
        }
      }

      function circle(x, y, r) {
        ctx.beginPath();
        ctx.arc(x, y, r, 0, Math.PI * 2, true);
        ctx.fill();
      }

      function clear() {
        ctx.clearRect(0, 0, width, height);
      }

      function draw(x, y) {
        clear(width, height);
        ctx.fillStyle = '#766';
        circle(x, y, r);
      }
      
      anim();*/
      const canvas = document.getElementById('playerCanvas'),
            board = document.getElementById('playBoard'),
            ctx = canvas.getContext('2d'),
            width = board.offsetWidth,
            height = board.offsetHeight;

      canvas.width = width;
      canvas.height = height;

      let curPoint = {
          x : this.user[0].pos.cur.x,
          y : this.user[0].pos.cur.y,
          index : 0   
      }

      let points = this.user[0].pos.points;
          
      function toPoints(points){
          let targetPoint = points[curPoint.index];
          let tx = targetPoint.x - curPoint.x,
              ty = targetPoint.y - curPoint.y,
              dist = Math.sqrt(tx*tx+ty*ty),
              rad = Math.atan2(ty,tx),
              angle = rad/Math.PI * 180;
           
          let velX = (tx/dist)*1,
              velY = (ty/dist)*1;

          curPoint.x += velX;
          curPoint.y += velY;

          if(dist < 2){
              curPoint.index++;
          }
          
          ctx.clearRect(0, 0, width, height);
          ctx.fillStyle = '#766';
          ctx.beginPath();
          ctx.arc(curPoint.x, curPoint.y, 15, 0, Math.PI * 2, false);
          ctx.fill();
          
          if(curPoint.index < points.length){
              setTimeout(function(){toPoints(points)}, 1);
          }
      }

      toPoints(points);
    },
    initDice(val) {
      this.steps = val;
      this.moveLinear();
    },
    moveLinear() {
      let idStart = this.user[0].id || 0,
          idFin = this.steps + idStart,
          rowStart, orderStart,
          rowFin, orderFin,
          x1, x2, y1, y2;

      if (idFin >= this.gameFile.length) {
        idFin = 99;
      }

      rowStart = this.gameFile[idStart].row;
      orderStart = this.gameFile[idStart].order;
      rowFin = this.gameFile[idFin].row;
      orderFin = this.gameFile[idFin].order;

      this.user[0].id = idFin;

      x1 = this.user[0].pos.points[this.user[0].pos.points.length - 1].x || 0;
      y1 = this.user[0].pos.points[this.user[0].pos.points.length - 1].y || 30;
      x2 = this.getCellPosition(idFin).x;
      y2 = this.getCellPosition(idFin).y;

      this.user[0].pos.cur.x = x1;
      this.user[0].pos.cur.y = y1;

      this.user[0].pos.points = [];

      if (rowStart != rowFin) {
        if (orderStart != this.rowArray[rowStart].length - 1) {
          this.user[0].pos.points.push(this.getCellPosition(null, rowStart, this.rowArray[rowStart].length - 1));
          this.user[0].pos.points.push(this.getCellPosition(null, rowFin, 0));
          if (this.gameFile[idFin].state === this.stateGood ||
              this.gameFile[idFin].state === this.stateBad) {
            this.moveNotLinear();
          } else {
            this.user[0].pos.points.push(
              { x: x2, y: y2 }
            );
          }
        } else {
          this.user[0].pos.points.push(this.getCellPosition(null, rowFin, 0));
          if (this.gameFile[idFin].state === this.stateGood ||
            this.gameFile[idFin].state === this.stateBad) {
            this.moveNotLinear();
          } else {
            this.user[0].pos.points.push(
              { x: x2, y: y2 }
            );
          }
        }
      } else {
        if (this.gameFile[idFin].state === this.stateGood ||
            this.gameFile[idFin].state === this.stateBad) {
          this.moveNotLinear();
        } else {
          this.user[0].pos.points.push(
            { x: x2, y: y2 }
          );
        }
      }

      this.canvasPlayer();
    },
    moveNotLinear() {
      let idStart = this.user[0].id,
          index = _.findIndex(this.lineCoords.start, ['id', idStart]),
          idFin = this.lineCoords.fin[index].id,
          x1, y1, x2, y2;

      this.user[0].id = idFin;

      x1 = this.getCellPosition(idStart).x;
      x2 = this.getCellPosition(idFin).x;
      y1 = this.getCellPosition(idStart).y;
      y2 = this.getCellPosition(idFin).y;

      this.user[0].pos.points.push(
        { x: x1, y: y1 }, 
        { x: x2, y: y2 }
      );
    },
    doSteps() {
      let arr = this.gameFile,
          obj = _.find(arr, x => { return x.player }),
          objEnd, state, stateId;

      var setPlayerPos = (index) => {
        this.playerPos.push({
          x: this.getCellPosition(arr[index].id).x,
          y: this.getCellPosition(arr[index].id).y
        })
      };

      var setEnd = (start) => {
        objEnd = _.find(arr, { 'stateId': stateId, 'state': `${state}-end` });
        arr[start].player = false;
        arr[objEnd.id].player = true;
      }

      var checkCondition = (index) => {
        if (arr[index].state == this.stateGood || arr[index].state == this.stateBad) {
          state = arr[index].state;
          stateId = arr[index].stateId;

          setEnd(index);

          setPlayerPos(objEnd.id);
          this.flag = true;
          this.canvasPlayer();
          this.flag = false;
        }
      }

      if (!obj) {
        arr[this.steps - 1].player = true;

        setPlayerPos(this.steps - 1);
        this.canvasPlayer();

        checkCondition(this.steps - 1);

      } else {
        if (obj.id + this.steps < this.gameFile.length - 1) {
          arr[obj.id].player = false;
          arr[obj.id + this.steps].player = true;

          setPlayerPos(obj.id + this.steps);
          this.canvasPlayer();

          checkCondition(obj.id + this.steps);

        } else {
          arr[obj.id].player = false;
          arr[this.gameFile.length - 1].player = true;

          setPlayerPos(this.gameFile.length - 1);
          this.canvasPlayer();

          this.$emit('finGame');
        }
      }
    },
    setPLayer() {
      for (let i = 0; i < this.playerCount; i++) {
        this.user.push({
          id: null,
          pos: {
            cur: {
              x: 0,
              y: 30
            },
            points: [
              {
                x: null,
                y: null
              }
            ]
          }
        });
      }
    }
  },
  created () {
    this.init();
  },
  mounted () {
    this.createWays();
    this.setPLayer();
  },
  computed: {
    rowArray: function() {
      return _.chunk(this.gameFile, this.boardSize);
    },
    emptyCells: function() {
      return _.filter(this.gameFile, ['empty', true]);
    },
    notEmptyCells: function() {
      return _.filter(this.gameFile, ['empty', false]);
    }
  },
  components: {
    Dice
  }
}
</script>
<template>
	<div class="dice__wrapper" @click="startStop">
		<div 
			class="dice"
			v-for="item in this.dice" 
			v-html="item.val"
		></div>
	</div>
</template>

<script>
export default {
	name: 'Dice',
	props: {
		diceCount: {
			type: Number
		}
	},
	data () {
		return {
			dice: [],
			dices: ['&#9856;', '&#9857;', '&#9858;', '&#9859;', '&#9860;', '&#9861;'],
			stopped: true,
			roll: null,
			rand: [],
			result: 0
		}
	},
	methods: {
		setDice() {
			for (let i = 0; i < this.diceCount; i++) {
				this.dice.push({
					val: this.dices[0]
				});

				this.rand.push({
					numb: 0
				});
			}
		},
		change() {
			for (let i = 0; i < this.rand.length; i++) {
				this.rand[i].numb = _.random(0, 5);
			}

			for (let i = 0; i < this.dice.length; i++) {
				this.dice[i].val = this.dices[this.rand[i].numb];
			}
		},
		startStop () {
			this.result = 0;
			for (let i = 0; i < this.rand.length; i++) {
				this.result += this.rand[i].numb;
			}
			this.result += this.rand.length;

			if(this.stopped) {
				this.stopped = false;
				this.roll = setInterval(this.change, 100);
			} else {
				clearInterval(this.roll);
				this.$emit('dice-result', this.result);
				this.getResult;
				this.stopped = true;
			}
		}
	},
	mounted () {
		this.setDice();
	}
}
</script>
<template>
	<div class="dice__wrapper" @click="startStop">
		<div class="dice" v-html="dice1"></div>
		<div class="dice" v-html="dice2"></div>
	</div>
</template>

<script>
export default {
	name: 'Dice',
	data () {
		return {
			dice1: '&#9856;',
			dice2: '&#9856;',
			dices: ['&#9856;', '&#9857;', '&#9858;', '&#9859;', '&#9860;', '&#9861;'],
			stopped: true,
			roll: null,
			rand1: null,
			rand2: null
		}
	},
	methods: {
		change() {
			this.rand1 = _.random(0, 5);
			this.rand2 = _.random(0, 5);

			this.dice1 = this.dices[this.rand1];
			this.dice2 = this.dices[this.rand2];
		},
		startStop () {
			if(this.stopped) {
				this.stopped = false;
				this.roll = setInterval(this.change, 100);
			} else {
				clearInterval(this.roll);
				this.$emit('dice-result', (this.rand1 + 1) + (this.rand2 + 1));
				this.getResult;
				this.stopped = true;
			}
		}
	}
}
</script>
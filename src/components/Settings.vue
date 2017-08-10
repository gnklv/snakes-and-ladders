<template>
	<div class="settings__wrapper">

		<div class="settings__item" v-for="point in settings">
			<label class="settings__label">{{ point.label }}</label>

			<div class="settings-radio__wrapper">
				<div class="settings-radio__item" v-for="item in types[point.key]">
					<input class="settings-radio__input" 
						:id="point.key + item.val" 
						:value="item.val"
						@change="setResult"
						v-model="point.val"
						type="radio"
					>
					<label class="settings-radio__label" :for="point.key + item.val">
						{{ item.text }}
					</label>
				</div>
			</div>
		</div>

	</div>
</template>

<script>
export default {
	name: 'Settings',
	data () {
		return {
			dropbox: {
				flag: null
			},
			settings: [
				{
					label: 'Размер доски',
					flag: 'size-board',
					key: 'board',
					val: 10
				},
				{
					label: 'Количество игроков',
					flag: 'players-count',
					key: 'player',
					val: 1
				},
				{
					label: 'Количество кубиков',
					flag: 'dice-count',
					key: 'dice',
					val: 1
				}
			],
			types: {
				board: [
					{
						text: '10 x 10',
						val: 10,
						active: false
					},
					{
						text: '15 x 15',
						val: 15,
						active: false
					},
					{
						text: '20 x 20',
						val: 20,
						active: false
					}
				],
				player: [
					{
						text: '1',
						val: 1,
						active: false
					},
					{
						text: '2',
						val: 2,
						active: false
					}
				],
				dice: [
					{
						text: '1',
						val: 1,
						active: false
					},
					{
						text: '2',
						val: 2,
						active: false
					},
					{
						text: '3',
						val: 3,
						active: false
					}
				]
			}
		}
	},
	methods: {
		setResult () {
			this.$emit('settings-result', _.mapValues(this.settings, x => { return x.val }));
		}
	}
}
</script>
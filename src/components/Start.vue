<template>
	<div class="start__centroid">
		<button class="start__btn" @click="$emit('startGame')">Новая игра</button>
	</div>
</template>

<script>
export default {
	name: 'Start'
}
</script>
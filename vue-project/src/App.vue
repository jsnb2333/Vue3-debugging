<script setup>
import { ref, watch } from 'vue';
const count = ref(0);
const count2 = ref(0);

function countAdd() {
	count.value++;
	count2.value++;
}

debugger
watch(() => {
	return count.value + count2.value
}, () => {
	console.log('执行了');
},{flush:'sync'});
</script>

<template>
	<div>
		<button @click="countAdd">
			{{ count }}
		</button>
	</div>
</template>

<style scoped>
header {
	line-height: 1.5;
}

.logo {
	display: block;
	margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
	header {
		display: flex;
		place-items: center;
		padding-right: calc(var(--section-gap) / 2);
	}

	.logo {
		margin: 0 2rem 0 0;
	}

	header .wrapper {
		display: flex;
		place-items: flex-start;
		flex-wrap: wrap;
	}
}
</style>

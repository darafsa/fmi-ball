<template>
	<div class="container">
		<Seat v-for="index in seats" v-bind:key="index" class="occupied" />
		<Seat :price="1" v-for="index in (seatsMax-seats >= 0 ? seatsMax-seats : 0)" v-bind:key="index" @click="process(index)" class="free" :class="{ selected: data.selected.has(index) }"></Seat>
	</div>
</template>

<script>
import { reactive } from '@vue/runtime-core'
import Seat from './Seat.vue'
export default {
	components: { Seat },
	props: {
		seats: Number,
		seatsMax: {
			default: 40,
			type: Number
		},
		modelValue: {
			default: new Set(),
			type: Set
		}
	},
	setup (props) {
		const data = reactive({
			selected: props.modelValue
		})
		
		function process(index) {
			if(data.selected.has(index)) {
				data.selected.delete(index)
			} else {
				data.selected.add(index)
			}
		}

		function print(str) {
			console.log(str)
		}

		return {
			data,
			print,
			process
		}
	}
}
</script>

<style lang="scss" scoped>
.container {
	display: grid;
	grid-template-rows: auto auto auto auto auto auto auto auto;
	grid-auto-flow: column;
}
</style>
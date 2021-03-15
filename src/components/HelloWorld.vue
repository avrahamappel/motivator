<template>
	<div>Today you made so far:
		<br>${{ moneyMade }}</div>
</template>

<script lang="ts">
import moment, { MomentInput } from 'moment';
import { defineComponent } from 'vue';

const calculateCurrentLogHours = (currentLogStarted: MomentInput) =>
	Math.abs(moment(currentLogStarted).diff(moment(), 'seconds')) / (60 * 60);

const calculateCurrentHours = (h: { hoursToday: number; currentLogStarted: MomentInput }) =>
	h.hoursToday + calculateCurrentLogHours(h.currentLogStarted);

export default defineComponent({
	data: () => ({
		// TODO: providers
		wage: 60,

		hoursProvider: {
			hoursToday: 0,
			currentLogStarted: '2021-03-14 21',
		},

		currentHours: 0,

		timeoutId: 0,
	}),

	created() {
		const update = () => this.currentHours = calculateCurrentHours(this.hoursProvider);
		update();
		this.timeoutId = setInterval(update, 100);
	},

	unmounted() {
		clearInterval(this.timeoutId);
	},

	computed: {
		moneyMade(): string {
			return (this.wage * this.currentHours).toFixed(2)
		},
	},

	watch: {
		// Change the document title to reflect the current sum.
		moneyMade(value) {
			document.title = '$' + value;
		}
	}
})
</script>

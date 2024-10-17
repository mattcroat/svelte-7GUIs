<script lang="ts">
	let elapsed = $state(0)
	let duration = $state(0)

	$effect(() => {
		if (!duration) return

		const interval = setInterval(() => {
			elapsed += 0.1
			if (elapsed > duration) clearInterval(interval)
		}, 100)

		return () => clearInterval(interval)
	})
</script>

<div class="grid-gap">
	<div>
		<label>
			<span>Elapsed time:</span>
			<progress max={duration} value={elapsed}></progress>
		</label>

		<div>{elapsed.toFixed(1)}s</div>
	</div>

	<label>
		<span>Duration:</span>
		<input type="range" bind:value={duration} min="1" max="20" />
	</label>

	<button onclick={() => (elapsed = 0)}>Reset</button>
</div>

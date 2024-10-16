<script lang="ts">
	let elapsed = $state(0)
	let duration = $state(0)

	$effect(() => {
		if (!elapsed && !duration) return

		const interval = setInterval(() => {
			if (elapsed < duration) {
				elapsed += 0.1
			} else {
				clearInterval(interval)
			}
		}, 100)

		return () => clearInterval(interval)
	})
</script>

<div>
	<label>
		<span>Elapsed time:</span>
		<progress max={duration} value={elapsed}></progress>
	</label>

	<p>{elapsed.toFixed(1)}s</p>

	<label>
		<span>Duration:</span>
		<input type="range" bind:value={duration} min="1" max="20" />
	</label>

	<div>
		<button onclick={() => (elapsed = 0)}>Reset</button>
	</div>
</div>

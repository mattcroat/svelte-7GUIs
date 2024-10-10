<script lang="ts">
	type Circle = {
		cx: number
		cy: number
		r: number
	}

	let circles = $state<Circle[]>([])
	let hovered = $state(false)	
	let selected = $state<number>()
	let adjusting = $state(false)
	let undos = $state<Circle[][]>([])
	let redos = $state<Circle[][]>([])

	$inspect(circles)

	function drawCircle(e: MouseEvent) {
    if (hovered) return

    if (adjusting) {
			adjusting = false
      return
    }

		const svgEl = e.target as SVGElement
		const { left, top } = svgEl.getBoundingClientRect()
		const cx = +(e.clientX - left).toFixed()
		const cy = +(e.clientY - top).toFixed()
		const r = 40

		undos.push($state.snapshot(circles))
		circles.push({ cx, cy, r })
	}

	function undo() {
		const snapshot = undos.pop()!
		redos.push(circles)
		circles = snapshot
	}

	function redo() {
		const snapshot = redos.pop()!
		circles = snapshot
		undos.push(snapshot)
	}
</script>

<div class="space-x">
	<div class="actions flex-center">
		<button onclick={undo}>Undo</button>
		<button onclick={redo}>Redo</button>
	</div>

	{#if adjusting}
		<div class="adjust surface-2 space-x">
			<span>Adjust diameter of circle at ({circles[selected!].cx}, {circles[selected!].cy})</span>
			<input type="range" bind:value={circles[selected!].r} />
		</div>
	{/if}

	<!-- svelte-ignore a11y_click_events_have_key_events a11y_no_static_element_interactions -->
	<svg
		onclick={drawCircle}
		width="600"
		height="600"
		viewBox="0 0 600 600"
		role="button"
		tabindex="0"
	>
		{#each circles as circle, i}
			<!-- svelte-ignore a11y_no_static_element_interactions -->
			<circle
				{...circle}
				fill={selected === i ? '#444' : 'transparent'}
				stroke="#fff"
				stroke-width="2"
				onclick={() => {
					selected = i
				}}
				oncontextmenu={(e) => {
					e.preventDefault()
					selected = i
					if (!adjusting) {
						undos.push($state.snapshot(circles))
					}
					adjusting = true
				}}
				onpointerenter={() => (hovered = true)}
				onpointerleave={() => (hovered = false)}
			/>
		{/each}
	</svg>
</div>

<style>
	svg {
		border: 2px solid #fff;
	}

	.adjust {
		width: 400px;
		position: absolute;
		top: 50%;
		left: 50%;
		translate: -50% -50%;
		padding: 1rem;
	}
</style>

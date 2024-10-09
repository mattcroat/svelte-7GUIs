<script lang="ts">
	type Options = "one-way" | "return"

	function getDate() {
		const date = new Date()
		const [month, day, year] = date
			.toLocaleDateString("en-US", {
				year: "numeric",
				month: "2-digit",
				day: "2-digit",
			})
			.split("/")
		return `${year}-${month}-${day}`
	}

	function handleSubmit(e: Event) {
		e.preventDefault()
		alert(`You have booked a ${selected} flight on ${startDate}.`)
	}

	let selected = $state<Options>("one-way")
	let startDate = $state(getDate())
	let returnDate = $state(getDate())
</script>

<form onsubmit={handleSubmit} class="container space" style:--width="200px">
	<select bind:value={selected}>
		<option value="one-way">one-way flight</option>
		<option value="return">return flight</option>
	</select>

	<label>
		<span class="screen-reader-text">Select the start date:</span>
		<input type="date" bind:value={startDate} min={getDate()} required />
	</label>

	<label>
		<span class="screen-reader-text">Select the return date:</span>
		<input
			type="date"
			bind:value={returnDate}
			min={getDate()}
			disabled={selected !== "return"}
			required
		/>
	</label>

	<button
		type="submit"
		disabled={!startDate || (selected === "return" && returnDate < startDate)}
	>
		Book
	</button>
</form>

<style>
	select,
	input,
	button {
		width: 100%;
	}
</style>

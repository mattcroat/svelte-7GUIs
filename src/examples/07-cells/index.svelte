<script lang="ts">
	type Cell = {
		row: number
		col: number
	}

	const rows = 4
	const cols = 4
	const letters = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'

	const data = $state([
		[{ value: 'Item' }, { value: 'Price (€)' }, { value: 'Amount (kg)' }, { value: 'Total' }],
		[{ value: '🍌' }, { value: '1' }, { value: '2' }, { value: '=MULTIPLY(B2,C2)' }],
		[{ value: '🍎' }, { value: '2' }, { value: '2' }, { value: '=MULTIPLY(B3,C3)' }],
		[{ value: '' }, { value: 'Weight (kg)' }, { value: '=SUM(C2,C3)' }, { value: '' }],
	])
	let selectedCell = $state()
	let editedCell = $state()

	function parse(value: string) {
		if (value.startsWith('=')) {
			const { operation, cells } = parseFormula(value)

			if (operation === 'SUM') {
				return sum(cells)
			}

			if (operation === 'MULTIPLY') {
				return multiply(cells)
			}
		}

		return value
	}

	function parseFormula(value: string) {
		// =MULTIPLY(B2,C2)
		const [a, b] = value.split('(')
		const operation = a.split('=')[1]
		const cells = b.replace(')', '').split(',')
		return { operation, cells: cells.map(cellNameToIndex) }
	}

	function cellNameToIndex(value: string) {
		// A1 -> 00 -> data[0][0]
		const [a, b] = value.split('')
		return { row: +b - 1, col: letters.indexOf(a) }
	}

	function sum(cells: Cell[]) {
		return cells.reduce((sum, { row, col }) => sum + +data[row][col].value, 0)
	}

	function multiply(cells: Cell[]) {
		return cells.reduce((product, { row, col }) => product * +data[row][col].value, 1)
	}

	function update(e: Event) {
		const { value, parentElement } = e.target as HTMLInputElement
		const { row, col } = cellNameToIndex(parentElement!.dataset.cell!)

		if (data[row]) {
			if (data[row][col]) {
				// update current cell
				data[row][col].value = value
			} else {
				// create new cell
				data[row][col] = { value }
			}
		} else {
			// create row
			data[row] = []
			data[row][col] = { value }
		}
	}
</script>

<table>
	<thead>
		<tr>
			<th></th>
			{#each Array(rows) as _, i}
				<th>{letters[i]}</th>
			{/each}
		</tr>
	</thead>

	<tbody>
		{#each Array(rows) as _, i}
			<tr>
				<th>{i + 1}</th>
				{#each Array(cols) as _, j}
					{@const cell = `${letters[j]}${i + 1}`}
					{@const value = data[i]?.[j]?.value}
					{@const parsedValue = value ? parse(value) : ''}
					{@const selected = selectedCell === cell}
					{@const editing = editedCell === cell}
					<td
						class:selected
						data-cell={cell}
						onclick={() => {
							if (selected) return
							selectedCell = cell
							editedCell = null
						}}
						ondblclick={() => (editedCell = cell)}
					>
						{#if editing}
							<!-- svelte-ignore a11y_autofocus -->
							<input type="text" {value} oninput={update} autofocus />
						{:else}
							<span>{parsedValue}</span>
						{/if}
					</td>
				{/each}
			</tr>
		{/each}
	</tbody>
</table>

<style>
	table {
		width: 480px;

		td {
			min-width: 100px;
			height: 40px;
			font-weight: 600;
			text-align: left;

			&.selected {
				outline: 1px solid aqua;
				border-radius: var(--radius-1);
			}

			input {
				height: 100%;
				padding: 0px;
				background: none;
				outline: none;
			}
		}
	}
</style>

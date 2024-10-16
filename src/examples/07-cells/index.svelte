<script lang="ts">
	const rows = 4
	const cols = 4
	const letters = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'

	const data = $state([
		[{ value: 'Item' }, { value: 'Price' }, { value: 'Amount' }, { value: 'Total' }],
		[{ value: '🍌' }, { value: '1' }, { value: '4' }, { value: '=MULTIPLY(B2,C2)' }],
		[{ value: '🍎' }, { value: '2' }, { value: '2' }, { value: '=MULTIPLY(B3,C3)' }],
		[{ value: '' }, { value: '' }, { value: 'Total' }, { value: '=SUM(D2,D3)' }],
	])
	let selectedCell = $state()
	let editedCell = $state()

	function parse(value: string): string | number {
		if (value.startsWith('=')) {
			const { operation, cells } = parseFormula(value)

			// we need to calculate the result from a formula
			const values = cells.map(({ row, col }) => {
				const value = data[row][col].value
				if (value.startsWith('=')) {
					return +parse(value)
				}
				return +value
			})

			return values.reduce(
				(total, value) => {
					if (operation === 'SUM') return total + value
					if (operation === 'MULTIPLY') return total * value
					return total
				},
				operation === 'MULTIPLY' ? 1 : 0,
			)
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
		const [col, ...row] = value.split('')
		return { row: +row.join('') - 1, col: letters.indexOf(col) }
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
	td {
		width: 160px;
		height: 40px;
		font-weight: 600;
		text-align: left;

		&.selected {
			outline: 1px solid var(--highlight);
			border-radius: var(--radius-1);
		}

		input {
			height: 100%;
			padding: 0px;
			background: none;
			outline: none;
		}
	}
</style>

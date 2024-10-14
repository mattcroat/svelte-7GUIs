<script lang="ts">
	type Cell = {
		row: number
		col: number
	}

	const rows = 4
	const cols = 4
	const alphabet = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'

	const data = $state([
		[{ value: 'Item' }, { value: 'Price' }, { value: 'Quantity' }, { value: 'Total' }],
		[{ value: 'Banana' }, { value: '2' }, { value: '4' }, { value: '=MULTIPLY(B2,C2)' }],
	])
	let selected = $state()
	let editing = $state()

	function parse(value: string) {
		if (value.startsWith('=')) {
			const { operation, cells } = parseFormula(value)
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
		return { row: +b - 1, col: alphabet.indexOf(a) }
	}

	function multiply(cells: Cell[]) {
		return cells.reduce((total, { row, col }) => {
			return total * +data[row][col].value
		}, 1)
	}
</script>

<table>
	<thead>
		<tr>
			<th></th>
			{#each Array(rows) as _, i}
				<th>{alphabet[i]}</th>
			{/each}
		</tr>
	</thead>

	<tbody>
		{#each Array(rows) as _, i}
			<tr>
				<th>{i + 1}</th>
				{#each Array(cols) as _, j}
					{@const cell = `${alphabet[j]}${i + 1}`}
					{@const value = data[i]?.[j]?.value}
					<td
						class:selected={selected === cell}
						data-cell={cell}
						onclick={() => {
							if (selected === cell) return
							selected = cell
							editing = null
						}}
						ondblclick={() => (editing = cell)}
					>
						{#if editing === cell}
							<input type="text" {value} />
						{:else}
							<span>{value ? parse(value) : null}</span>
						{/if}
					</td>
				{/each}
			</tr>
		{/each}
	</tbody>
</table>

<style>
	table {
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

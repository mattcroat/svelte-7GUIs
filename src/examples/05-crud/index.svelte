<script lang="ts">
	type Person = {
		name: string
		surname: string
	}

	let people = $state([
		{ name: 'Rich', surname: 'Harris' },
		{ name: 'Ryan', surname: 'Carniato' },
		{ name: 'Evan', surname: 'You' },
	])

	let prefix = $state('')
	const filteredPeople = $derived(
		prefix ? people.filter((person) => person.surname.toLowerCase().startsWith(prefix)) : people,
	)
	let selected = $state<Person>()
	let person = $state({ name: '', surname: '' })

	$effect(() => {
		person = {
			name: selected?.name ?? '',
			surname: selected?.surname ?? '',
		}
	})

	function createPerson() {
		people.push(person)
		person = { name: '', surname: '' }
	}

	function updatePerson() {
		const index = people.findIndex(
			(user) => user.name === selected?.name && user.surname === selected?.surname,
		)
		people[index] = { name: person.name, surname: person.surname }
	}

	function deletePerson() {
		people = people.filter(
			({ name, surname }) => name !== person.name && surname !== person.surname,
		)
		person = { name: '', surname: '' }
	}
</script>

<div class="crud">
	<div class="search">
		<label>
			<span>Filter prefix:</span>
			<input type="text" bind:value={prefix} />
		</label>
	</div>

	<label class="people">
		<span class="screen-reader-text">Names:</span>
		<select bind:value={selected} size="5">
			{#each filteredPeople as user}
				<option value={user}>{user.surname}, {user.name}</option>
			{/each}
		</select>
	</label>

	<div class="details">
		<label>
			<span>Name:</span>
			<input type="text" bind:value={person.name} />
		</label>

		<label>
			<span>Surname:</span>
			<input type="text" bind:value={person.surname} />
		</label>
	</div>

	<div class="actions">
		<button onclick={createPerson}>Create</button>
		<button onclick={updatePerson}>Update</button>
		<button onclick={deletePerson}>Delete</button>
	</div>
</div>

<style>
	.crud {
		width: 500px;
		display: grid;
		grid-template-areas:
			'search .'
			'people details'
			'actions actions';
		grid-template-columns: 240px 1fr;
		grid-auto-rows: auto;
		gap: 1rem;
		padding: 1rem;
		background-color: var(--gray-8);
		box-shadow: var(--shadow-2);
		border-radius: var(--radius-2);

		.search {
			grid-area: search;

			label {
				display: grid;
				grid-template-columns: 2fr 1fr;
				align-items: baseline;
			}
		}

		.people {
			grid-area: people;
		}

		.details {
			grid-area: details;
			display: grid;
			grid-template-columns: auto 1fr;
			grid-template-rows: repeat(2, auto) 1fr;
			align-items: baseline;
			gap: 0.5rem;

			label {
				display: contents;
			}
		}

		.actions {
			grid-area: actions;
		}
	}
</style>

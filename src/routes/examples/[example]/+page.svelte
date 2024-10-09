<script lang="ts">
	import { page } from '$app/stores'

	const example = $derived(import(`../../../examples/${$page.params.example}/index.svelte`))
</script>

{#await example then component}
	<component.default />
{/await}

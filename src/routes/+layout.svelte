<script lang="ts">
	import { fly } from 'svelte/transition'
  import { page } from '$app/stores'
  import '../app.css'

  const examples = [
    { label: 'Counter', path: '01-counter' },
    { label: 'Temperature converter', path: '02-temperature-converter' },
    { label: 'Flight booker', path: '03-flight-booker' },
    { label: 'Timer', path: '04-timer' },
    { label: 'CRUD', path: '05-crud' },
    { label: 'Circle drawer', path: '06-circle-drawer' },
    { label: 'Cells', path: '07-cells' },
  ]

  let { children } = $props()
  let open = $state(false)
</script>

<svelte:head>
  <title>7GUIs</title>
</svelte:head>

{#snippet svelte()}
  <svg xmlns="http://www.w3.org/2000/svg" width="48" height="48" viewBox="0 0 107 128">
    <title>Svelte logo</title>
    <path d="M94.1566,22.8189c-10.4-14.8851-30.94-19.2971-45.7914-9.8348L22.2825,29.6078A29.9234,29.9234,0,0,0,8.7639,49.6506a31.5136,31.5136,0,0,0,3.1076,20.2318A30.0061,30.0061,0,0,0,7.3953,81.0653a31.8886,31.8886,0,0,0,5.4473,24.1157c10.4022,14.8865,30.9423,19.2966,45.7914,9.8348L84.7167,98.3921A29.9177,29.9177,0,0,0,98.2353,78.3493,31.5263,31.5263,0,0,0,95.13,58.117a30,30,0,0,0,4.4743-11.1824,31.88,31.88,0,0,0-5.4473-24.1157" style="fill:#ff3e00"/>
    <path d="M45.8171,106.5815A20.7182,20.7182,0,0,1,23.58,98.3389a19.1739,19.1739,0,0,1-3.2766-14.5025,18.1886,18.1886,0,0,1,.6233-2.4357l.4912-1.4978,1.3363.9815a33.6443,33.6443,0,0,0,10.203,5.0978l.9694.2941-.0893.9675a5.8474,5.8474,0,0,0,1.052,3.8781,6.2389,6.2389,0,0,0,6.6952,2.485,5.7449,5.7449,0,0,0,1.6021-.7041L69.27,76.281a5.4306,5.4306,0,0,0,2.4506-3.631,5.7948,5.7948,0,0,0-.9875-4.3712,6.2436,6.2436,0,0,0-6.6978-2.4864,5.7427,5.7427,0,0,0-1.6.7036l-9.9532,6.3449a19.0329,19.0329,0,0,1-5.2965,2.3259,20.7181,20.7181,0,0,1-22.2368-8.2427,19.1725,19.1725,0,0,1-3.2766-14.5024,17.9885,17.9885,0,0,1,8.13-12.0513L55.8833,23.7472a19.0038,19.0038,0,0,1,5.3-2.3287A20.7182,20.7182,0,0,1,83.42,29.6611a19.1739,19.1739,0,0,1,3.2766,14.5025,18.4,18.4,0,0,1-.6233,2.4357l-.4912,1.4978-1.3356-.98a33.6175,33.6175,0,0,0-10.2037-5.1l-.9694-.2942.0893-.9675a5.8588,5.8588,0,0,0-1.052-3.878,6.2389,6.2389,0,0,0-6.6952-2.485,5.7449,5.7449,0,0,0-1.6021.7041L37.73,51.719a5.4218,5.4218,0,0,0-2.4487,3.63,5.7862,5.7862,0,0,0,.9856,4.3717,6.2437,6.2437,0,0,0,6.6978,2.4864,5.7652,5.7652,0,0,0,1.602-.7041l9.9519-6.3425a18.978,18.978,0,0,1,5.2959-2.3278,20.7181,20.7181,0,0,1,22.2368,8.2427,19.1725,19.1725,0,0,1,3.2766,14.5024,17.9977,17.9977,0,0,1-8.13,12.0532L51.1167,104.2528a19.0038,19.0038,0,0,1-5.3,2.3287" style="fill:#fff"/>
  </svg>
{/snippet}

{#snippet arrow()}
  <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class:rotate={open} style="transition: rotate 0.3s">
    <path d="m9 18 6-6-6-6"/>
  </svg>
{/snippet}

<div class="container" class:grid={open}>
  <button onclick={() => open = !open} class="menu">
    {@render arrow()}
  </button>

  {#if open}    
    <aside transition:fly={{ x: '-400px' }}>
      <div class="nav-container space-y">
        <div class="title">
          {@render svelte()}
          <h2>7GUIs</h2>
        </div>

        <nav class="grid-gap">
          {#each examples as { label, path }, i}
          {@const current = $page.params.example === path ? 'page' : undefined}
            <a href="/examples/{path}" aria-current={current}>
              {i + 1}. {label}
            </a>
          {/each}
        </nav>
      </div>
    </aside>
  {/if}

  <main>
    {@render children()}
  </main>
</div>

<style>
  .container {
    height: 100%;

    .menu {
      padding: 0px;
      background: none;
      border: none;
      box-shadow: none;
      position: absolute;
      left: var(--size-7);
      bottom: var(--size-7);
      z-index: 10;
    }
    
    aside {
      width: 280px;
      position: absolute;
      top: 0px;
      bottom: 0px;
      left: 0px;
      border-right: 1px solid var(--gray-8);
      box-shadow: var(--shadow-6);
      backdrop-filter: contrast(98%) blur(20px);

      .nav-container {
        .title {
          display: flex;
          align-items: center;
          gap: var(--size-1);
          padding: var(--size-7);
          border-bottom: 1px solid var(--gray-8);
        }

        nav {
          padding-inline-start: var(--size-7);
        }
      }
    }

    a {
      font-weight: 600;
      color: var(--gray-5);
      text-decoration: none;
      text-transform: capitalize;

      &[aria-current='page'] {
        color: var(--highlight);
      }

      &:hover {
        color: var(--highlight);
      }
    }
  }

  .rotate {
    rotate: -0.5turn;
  }
</style>
<script>
  import { createEventDispatcher } from "svelte";
  import { blurOnKey } from "./util";

  export let item;

  let editing = false;
  const dispatch = createEventDispatcher();
</script>

<style>
  button {
    background-color: transparent;
    border: none;
  }

  input[type="checkbox"] {
    --size: 24px;
    height: var(--size);
    width: var(--size);
  }

  input[type="text"] {
    border: 1px solid lightgray;
  }

  li {
    display: flex;
    align-items: center;
  }

  .packed-true {
    color: gray;
    text-decoration: line-through;
  }

  span {
    margin: 0 10px;
  }
</style>

<li>
  <input type="checkbox" bind:checked={item.packed} />
  {#if editing}
    <input
      type="text"
      bind:value={item.name}
      on:blur={() => (editing = false)}
      on:keydown={blurOnKey} />
  {:else}
    <span
      class="packed-{item.packed}"
      on:click={() => (editing = true)}>{item.name}</span>
  {/if}
  <button class="icon" on:click={() => dispatch('delete')}>&#x1F5D1;</button>
</li>

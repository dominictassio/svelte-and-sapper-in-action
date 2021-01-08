<script>
  import { createEventDispatcher } from "svelte";
  import Category from "./Category.svelte";
  import { getGuid, sortOnName } from "./util";

  let categoryArray = [];
  let categories = {};
  let categoryName;
  let message = "";
  let show = "all";
  const dispatch = createEventDispatcher();

  $: categoryArray = sortOnName(Object.values(categories));

  function addCategory() {
    const duplicate = Object.values(categories).some(
      (category) => category.name === categoryName
    );
    if (duplicate) {
      message = `The category "${categoryName}" already exists.`;
      alert(message);
      return;
    }
    const id = getGuid();
    categories[id] = { id, name: categoryName, items: {} };
    categories = categories;
    categoryName = "";
  }

  function deleteCategory(category) {
    delete categories[category.id];
    categories = categories;
  }

  function clearAllChecks() {
    for (const category of Object.values(categories)) {
      for (const item of Object.values(category.items)) {
        item.packed = false;
      }
    }
    categories = categories;
  }

  restore();

  $: if (categories) {
    persist();
  }

  function persist() {
    localStorage.setItem("travel-packing", JSON.stringify(categories));
  }

  function restore() {
    const text = localStorage.getItem("travel-packing");
    if (text && text !== "{}") {
      categories = JSON.parse(text);
    }
  }
</script>

<style>
  .categories {
    display: inline-flex;
    flex-wrap: wrap;
    justify-content: center;
  }

  .clear {
    margin-left: 30px;
  }

  input[type="radio"] {
    --size: 24px;
    height: var(--size);
    width: var(--size);
    margin-left: 10px;
  }

  .logout-btn {
    position: absolute;
    right: 20px;
    top: 20px;
  }

  .radios {
    display: flex;
    align-items: center;
    border: none;
  }

  .radios > label + label {
    display: inline-flex;
    align-items: center;
    margin-left: 1em;
  }

  .radios > label > input {
    margin-bottom: -3px;
    margin-right: 5px;
  }

  section {
    display: flex;
    flex-direction: column;
    align-items: center;
    font-size: 24px;
    margin-top: 1em;
  }
</style>

<section>
  <header>
    <form on:submit|preventDefault={addCategory}>
      <label> New Category <input bind:value={categoryName} /> </label>
      <button disabled={!categoryName}>Add Category</button>
      <button class="logout-btn" on:click={() => dispatch('logout')}>Log Out</button>
    </form>
    <p>
      Suggested categories include Backpack, Clothes,
      <br />
      List Minute, Medicines, Running Gear, and Toiletries.
    </p>
    <fieldset class="radios">
      <legend>Show</legend>
      <label>
        <input name="show" type="radio" value="all" bind:group={show} />
        All
      </label>
      <label>
        <input name="show" type="radio" value="packed" bind:group={show} />
        packed
      </label>
      <label>
        <input name="show" type="radio" value="unpacked" bind:group={show} />
        Unpacked
      </label>
      <button class="clear" on:click={clearAllChecks}>Clear All Checks</button>
    </fieldset>
  </header>
  <div class="categories">
    {#each categoryArray as category (category.id)}
      <Category
        bind:category
        {categories}
        {show}
        on:delete={() => deleteCategory(category)}
        on:persist={persist} />
    {/each}
  </div>
</section>

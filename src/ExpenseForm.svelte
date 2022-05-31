<script>
//   import {onMount, onDestroy, beforeUpdate, afterUpdate} from 'svelte'
//   onMount(() =>{
//       console.log('form has mounted')
//   })
//   beforeUpdate(() =>{
//       console.count('before update')
//   })
//   afterUpdate(() =>{
//       console.count('after update')
//   })
//   onDestroy(() =>{
//       console.log('form is hidden')
//   })
  import Title from "./Title.svelte";
  export let name = ""; // passing as props so they can be modified from outside
  export let amount = null;
  export let addExpense;
  export let isEditing;
  export let editExpense;
  export let hideForm;
  // $: console.log({name, amount}) for testing
  // a truthy value is considered TRUE when encountered
  // in boolean context.
  // if either of them are empty then return isEmpty=true
  $: isEmpty = !name || !amount;
  function handleSubmit() {
    if (isEditing) {
      editExpense({ name, amount });
    } else {
      addExpense({ name, amount });
    }
    name = "";
    amount = null;
    hideForm()
  }
</script>

<section class="form">
  <Title title="add expense" />
  <form class="expense-form" on:submit|preventDefault={handleSubmit}>
    <div class="form-control">
      <label for="name">name</label>
      <input type="text" id="name" bind:value={name} />
    </div>
    <div class="form-control">
      <label for="amount">amount</label>
      <input type="number" id="amount" bind:value={amount} />
    </div>
    {#if isEmpty}
      <p class="form-empty">please fill out all form fields</p>
    {/if}
    <!-- make button disabled if isEmpty is true -->
    <button
      disabled={isEmpty}
      type="submit"
      class="btn btn-block"
      class:disabled={isEmpty}
    >
      {#if isEditing}
        edit expense
      {:else}
        add expense
      {/if}
    </button>
    <button type="button" class="close-btn" on:click={hideForm}>
      <i class="fas fa-times" />
      close
    </button>
  </form>
</section>

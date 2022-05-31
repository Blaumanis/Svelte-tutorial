<script>
  import { setContext, onMount, afterUpdate } from "svelte"; // get from svelte
  // components
//   import GitHub from "./GitHub.svelte";
  import GitHubAwait from "./GitHubAwait.svelte";
  import Navbar from "./Navbar.svelte";
  import ExpensesList from "./ExpensesList.svelte";
  import Totals from "./Totals.svelte";
  import ExpenseForm from "./ExpenseForm.svelte";
  import Modal from "./Modal.svelte";
  // data
//   import expensesData from "./expenses";
  // variables
  // spreading data in to expenses array
  let expenses = [];
  // set editing variables
  let setName = "";
  let setAmount = null;
  let setId = null;
  // toggle form variables
  let isFormOpen = false;
  // reactive
  $: isEditing = setId ? true : false; // checking if setId is empty
  $: total = expenses.reduce((acc, curr) => {
    return (acc += curr.amount);
  }, 0);
  // functions
  function showForm(){
	  isFormOpen = true;
  }
  function hideForm() {
	  isFormOpen = false;
	  setName = '';
	  setAmount = null;
	  setId = null;
  }
  function removeExpense(id) {
    // reasigning the value for expenses array, because filter method returns new array
    // if item.id does not match the id which is passed return the new array without that item
    expenses = expenses.filter((item) => item.id !== id);
	// overwrite expenses list in localstorage
  }
  function clearExpenses() {
    expenses = [];
  }
  function addExpense({ name, amount }) {
    let expense = { id: Math.random() * Date.now(), name, amount };
    expenses = [expense, ...expenses]; // putting on a top the new expense
  }
  function setModifiedExpense(id) {
    let expense = expenses.find((item) => item.id === id);
    setId = expense.id;
    setName = expense.name;
    setAmount = expense.amount;
	showForm();
  }
  function editExpense({ name, amount }) {
    console.log({ name, amount });
    expenses = expenses.map((item) => {
      return item.id === setId ? { ...item, name, amount } : { ...item };
    });
    setId = null;
    setName = "";
    setAmount = null;
  }
  // context
  setContext("remove", removeExpense);
  setContext("modify", setModifiedExpense);
//   local storage
function setLocalStorage() {
	localStorage.setItem('expenses', JSON.stringify(expenses))
}
// if localstorage is wih expenses then parse to JS object if localstorage is empty
//  return empty expenese array
onMount(() => {
	expenses = localStorage.getItem('expenses')?
	JSON.parse(localStorage.getItem('expenses'))
	:[]
})
// calling setLocalStorage fn every time we update form
afterUpdate(() => {
	setLocalStorage()
})
</script>
<!-- passing showForm to NavBar -->
<Navbar {showForm} /> 
<main class="content">
	<!-- <GitHubAwait/> -->
  {#if isFormOpen}
  <Modal>
    <ExpenseForm
      {addExpense}
      name={setName}
      amount={setAmount}
      {isEditing}
      {editExpense}
	  {hideForm} />
	</Modal>
  {/if}
  <Totals title="total expenses" {total} />
  <ExpensesList {expenses} />
  <button
    type="button"
    class="btn btn-primary btn-block"
    on:click={clearExpenses}>clear expenses</button
  >
</main>


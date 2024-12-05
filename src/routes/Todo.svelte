<script>
import {onMount} from 'svelte';
let todoItem = $state('');
let todoList = $state([]);
let doneList = $state([]);

onMount(() => {
     let storedList = localStorage.getItem('storedList');
     if (storedList) {
          todoList = (JSON.parse(storedList));
     }
})

function updateList() {
     return localStorage.setItem('storedList', JSON.stringify(todoList));
}

function addItem(event) {
     event.preventDefault();
     if (todoItem == '') {
          return;
     }
     todoList = [...todoList, {
          text: todoItem,
          done: false
     }];
     todoItem = '';
     updateList();
}
function removeItem(index) {
     todoList = todoList.toSpliced(index, 1);
     updateList();
}
function nuke() {
     todoList = [];
     localStorage.clear();
}
function clearDone() {
     todoList = todoList.filter((item) => !item.done);
     updateList();
}
/* $effect(() => {
     doneList = todoList.filter((item) => item.done);
     updateList();
})*/
/*function undoThis(item) {
     item.done = !item.done;
     updateList();  
}*/
// $inspect('To Do List: ', todoList);
//$inspect('Done List: ', doneList);
</script>
<form onsubmit={addItem}>
<input type="text" bind:value={todoItem}>
<button type="submit">Add</button>
</form>
<div class="lists">
     <div class="todo-list">
          <ul>
               {#each todoList as item, index}
                    <li class:done={item.done}>
                         <input type="checkbox" bind:checked={item.done} onchange={updateList}>
                         <span>{item.text}</span>
                         <button type="button" onclick={() => removeItem(index)}>x</button>
                    </li>
               {/each}
          </ul>
          {#if (todoList.length == 0)}
               <button disabled type="button">Nuclear Option</button>
          {:else}
               <button type="button" onclick={nuke}>Nuclear Option</button>
               <button type="button" onclick={clearDone}>Clear Done</button>
          {/if}
     </div>

     {#if (doneList.length > 0)}
     <div class="done-list">
          <h2>Done Items</h2>
          {#each doneList as item}
               <li>
                    <span>{item.text}</span>
                    <button type="button" onclick={()=> undoThis(item)}>undo</button>
               </li>
          {/each}
     </div>
     {/if}
</div>
<style>
form {
     display: flex;
}
input[type="text"] {
     border-top-left-radius: 50px;
     border-bottom-left-radius: 50px;
     padding: 0.6em 1rem;
     font-size: 1.2rem;
     border-right: none;
     display: inline-block;
}
form button {
     border-top-right-radius: 50px;
     border-bottom-right-radius: 50px;
     padding: 0.6em 1rem;
     font-size: 1.2rem;
     
     cursor: pointer;
     &:hover {
          background-color: #ddd;
     }
}
.lists {
     display: flex;
     justify-content: space-between;
     & > div {
          flex-basis: 40%;
          flex-shrink: 0;
     }
}

ul {
     list-style: none;
     padding: 0;
}
/*li.done {
     display: none;
}*/
li.done span {
     text-decoration: line-through;
}
li button {
     background-color: transparent;
}
</style>
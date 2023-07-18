<script>
  import { db } from "../../lib/firebase/firebase";
  import { authHandlers, authStore } from "../../store/store";
  import { getDoc, doc, setDoc } from 'firebase/firestore';

    let todoList = [];
    let currTodo = "";
    let error = false;

    authStore.subscribe(curr => {
        todoList = curr.data.todos;
    })

    function addTodo(){
     error = false;
        if(!currTodo){
            error = false;
        }
        todoList = [...todoList, currTodo];
        currTodo = "";
    }

    function editTodo(index){
        let newTodoList = [...todoList].filter((val,i) => {
            return i != index;
        });
        currTodo = todoList[index];
        todoList = newTodoList;
    }

    function removeTodo(index){
        let newTodoList = [...todoList].filter((val,i) => {
            return i != index;
        });
        todoList = newTodoList;
    }

    async function saveTodo(){
        try{
            const userRef = doc(db, 'users', $authStore.user.uid);
            await setDoc(
                userRef,
                {
                    todos: todoList,
                },
                { merge: true }
            );
        }catch (err){
            console.log("There was an error saving your information")
        }
    }

</script>
{#if !$authStore.loading}
    <div class="mainContainer">
        <div class="headerContainer">
            <h1>Todo List</h1>
            <div class="headerBtns">
                <button on:click={saveTodo}><i class="fa-regular fa-floppy-disk"></i><p>Save</p></button>
                <button on:click={authHandlers.logout}><i class="fa-solid fa-right-from-bracket"></i><p>Logout</p></button>
            </div>
        </div>
    <main>
        {#if todoList.length === 0}
            <p>
                You have nothing to do!
            </p>
        {/if}
        {#each todoList as todo, index}
            <div class="todo">
                <p>
                    {index + 1}. {todo}
                </p>
                <div class="actions">
                    <!-- svelte-ignore a11y-no-static-element-interactions -->
                    <!-- svelte-ignore a11y-click-events-have-key-events -->
                    <i on:click={() => {editTodo(index)}} class="fa-regular fa-pen-to-square"></i>
                    <!-- svelte-ignore a11y-click-events-have-key-events -->
                    <!-- svelte-ignore a11y-no-static-element-interactions -->
                    <i on:click={() => {removeTodo(index)}} class="fa-solid fa-trash-can"></i>
                </div>
            </div>
        {/each}
    </main>    
    </div>
    <div class={"enterTodo" + (error ? 'errorBorder' : "")}>
        <input bind:value={currTodo} type="text" placeholder="Enter todo" />
        <button on:click={addTodo}>ADD</button>
    </div>
{/if}
<style>
    .mainContainer{
        display: flex;
        flex-direction: column;
        min-height: 100vh;
        gap: 24px;
        padding: 24px;
        width: 100%;
        max-width: 2000px;
        margin: 0 auto;
    }

    .headerContainer{
        display: flex;
        align-items: center;
        justify-content: space-between;
    }

    .headerBtns{
        display: flex;
        align-items: center;
        gap: 14px;
    }

    .headerContainer button{
        background: #003c5b;
        color: white;
        padding: 10px 18px;
        border-radius: 4px;
        border: none;
        font-weight: 700;
        display: flex;
        align-items: center;
        gap: 10px;
        cursor: pointer;
    }

    .headerContainer button:hover{
        opacity: 0.7;
    }

    .headerContainer button i{
        font-size: 1.1rem;
    }

    main{
        display: flex;
        flex-direction: column;
        gap: 8px;
        flex: 1;
    }

    .todo{
        border-left: 1px solid cyan;
        padding: 4px 8px;
        display: flex;
        align-items: center;
        justify-content: space-between;
    }

    .actions{
        display: flex;
        align-items: center;
        gap: 14px;
        font-size: 1.2rem;
    }

    .actions i{
        cursor: pointer;
    }

    .enterTodo {
        display: flex;
        align-items: stretch;
        border: 1px solid #0891b2;
        border-radius: 5px;
        overflow: hidden;
    }

    .errorBorder{
        border-color: coral !important;
    }

    .enterTodo input {
        background: transparent;
        border: none;
        padding: 14px;
        color: white;
        flex: 1;
    }

    .enterTodo input:focus{
        outline: none;
    }

    .enterTodo button{
        padding: 0 14px;
        background: #003c5b;
        border: none;
        color: cyan;
        font-weight: 600;
        cursor: pointer;
    }

    .enterTodo button:hover{
        background: transparent;
    }

</style>
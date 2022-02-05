<script>
  import Todo from "./Todo.svelte";
  let todoList = [];
  let localTodos = localStorage.getItem("todos");
  if (localTodos) {
    todoList = [...JSON.parse(localTodos)];
  }
  let lastID = +localStorage.getItem("lastID") || 0;
  function generateID() {
    lastID += 1;
    localStorage.setItem("lastID", lastID + "");
    return lastID;
  }
  let completedTodosList = [];
  calculateCompletedTasks();
  function calculateCompletedTasks() {
    let indexArray = [];
    todoList.map((todo, index) => {
      if (todo.done === true) {
        indexArray.push(index);
      }
    });
    let output = [];
    for (let i = 0; i < indexArray.length; i++) {
      output.push(todoList[indexArray[i]]);
    }
    completedTodosList = [...output];
  }

  function saveTodos() {
    calculateCompletedTasks();
    localStorage.setItem("todos", JSON.stringify(todoList));
  }

  function addTodo(inputValue) {
    let genId = generateID();
    let todo = {
      task: inputValue,
      id: genId,
      done: false,
    };
    todoList = [...todoList, todo];
    saveTodos();
  }

  function checkInput(event) {
    if (event.key === "Enter") {
      if (event.target.value.trim().length > 2) {
        addTodo(event.target.value.trim());
        event.target.value = "";
      } else {
        alert(`Todo's text should be 3 characters long or more.`);
      }
    }
  }

  function getTodoIndex(detailId) {
    let todoIndex = -1;
    todoList.map((todo, index) => {
      if (todo.id === detailId) {
        todoIndex = index;
      }
    });
    return todoIndex;
  }

  function clearCompleted() {
    let output = todoList.filter((todo) => {
      return todo.done === false;
    });
    todoList = [...output];
    saveTodos();
  }

  function dealWithDelete(event) {
    let Index = getTodoIndex(event.detail);
    let copy = [...todoList];
    copy.splice(Index, 1);
    todoList = [...copy];
    saveTodos();
    if (todoList.length === 0) {
      localStorage.setItem("lastID", "0");
    }
  }

  function dealWithDone(event) {
    let Index = getTodoIndex(event.detail);
    todoList[Index].done = !todoList[Index].done;
    saveTodos();
  }

  function dealWithPosition(isUp, id) {
    let index = getTodoIndex(id);
    let secondIndex;
    if (isUp) {
      secondIndex = index - 1;
    } else {
      secondIndex = index + 1;
    }
    let copy = [...todoList];
    let tempObj = copy[secondIndex];
    copy[secondIndex] = copy[index];
    copy[index] = tempObj;
    todoList = [...copy];
  }
  function dealWithPositionUp(event) {
    dealWithPosition(true, event.detail);
  }

  function dealWithPositionDown(event) {
    dealWithPosition(false, event.detail);
  }
</script>

<header>
  <h1><span>Hello</span> Todo!</h1>
</header>
<main>
  <section>
    <div class="outline-container">
      <input
        class="outline-class"
        on:keydown={checkInput}
        type="text"
        placeholder="Write your TODO and hit 'Enter'"
      />

      <span class="focus-border">
        <i />
      </span>
    </div>
  </section>
  {#if todoList.length === 0}
    <div class="empty">
      <h2>
        We do not have <span class="any sad"> any</span> tasks from you
        <span class="any">:(</span>
      </h2>
    </div>
  {:else}
    <section id="list">
      <ul>
        {#each todoList as todo}
          <li>
            <Todo
              done={todo.done}
              id={todo.id}
              text={todo.task}
              listLength={todoList.length}
              index={todoList.indexOf(todo)}
              on:notifyDone={dealWithDone}
              on:notifyDelete={dealWithDelete}
              on:notifyPositionUp={dealWithPositionUp}
              on:notifyPositionDown={dealWithPositionDown}
            />
          </li>
        {/each}
      </ul>
    </section>

    <div class="clear-all-container">
      <div>
        {#if completedTodosList.length > 0}
          <p class="status">
            You have completed <span class="status-span"
              >{completedTodosList.length}</span
            >
            out of <span class="status-span">{todoList.length}</span>
            tasks.
          </p>
        {:else}
          <p class="status">
            You have <span class="status-span any">not </span>completed any
            tasks.
            <span class="status-span">{todoList.length}</span> tasks await you.
          </p>
        {/if}
      </div>
      {#if completedTodosList.length > 0}
        <button on:click={clearCompleted} class="clear-all-btn"
          >Clear all completed tasks&nbsp;&nbsp;&nbsp;<i
            class="fas fa-trash-alt"
          /></button
        >{/if}
    </div>
  {/if}
</main>

<footer>
  <p style="text-align: center; color:grey">Made by Przemysław Dylewski</p>
</footer>

<style>
  header {
    text-align: center;
    margin: 50px auto 20px;
  }

  .empty {
    min-height: 150px;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  li {
    list-style: none;
  }

  .status {
    background-color: rgb(206, 206, 206);
    border: 1px solid rgba(1, 105, 1, 1);
    padding: 6px;
    height: 35px;
  }

  .status-span {
    font-weight: bold;
  }

  #list {
    margin-top: 40px;
    max-height: 600px;
    scroll-behavior: inherit;
    background-color: rgba(1, 105, 1, 0.15);
    overflow-y: auto;
    border: 1px solid rgba(1, 105, 1, 1);
  }

  ul {
    padding: 5px;
  }

  .any {
    color: #b42e01;
  }
  .any.sad {
    text-decoration: underline;
  }

  main {
    width: 100%;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    margin-bottom: 30px;
  }
  #list {
    padding: 10px 0;
  }

  section {
    min-width: 515px;
    margin: 0 150px;
  }

  .clear-all-container {
    min-width: 515px;
    margin: 0 150px;
    margin-top: 5px;
    display: flex;
    align-items: center;
    justify-content: space-between;
  }
  .clear-all-btn {
    margin: 0;
    transition: color 0.3s, background-color 0.3s, border-color 0.3s;
    background-color: rgb(206, 206, 206);
    border-radius: 0;
    border: 1px solid rgba(1, 105, 1, 1);
    cursor: pointer;
    height: 35px;
  }
  .clear-all-btn:hover,
  .clear-all-btn:active {
    background-color: #333;
    color: #b42e01;
    border-color: black;
  }

  input {
    outline: none;
    padding: 10px 25px;
    width: 100%;
  }

  h1 {
    color: #b42e01;
    text-transform: uppercase;
    font-size: 4em;
    font-weight: 100;
  }

  h1 span {
    color: #4d4c4c;
  }

  /* @media (min-width: 640px) {
    main {
      max-width: none;
    }
  } */

  .outline-container {
    position: relative;
  }

  .outline-class {
    border: 1px solid #c8c8c8;
    margin: 0;
    transition: 0.4s;
  }
  .outline-class ~ .focus-border:before,
  .outline-class ~ .focus-border:after {
    content: "";
    position: absolute;
    top: 0;
    right: 0;
    width: 0;
    height: 2px;
    background-color: rgba(1, 105, 1, 1);
    transition: 0.2s;
    transition-delay: 0.2s;
  }
  .outline-class ~ .focus-border:after {
    top: auto;
    bottom: 0;
    right: auto;
    left: 0;
    transition-delay: 0.6s;
  }
  .outline-class ~ .focus-border i:before,
  .outline-class ~ .focus-border i:after {
    content: "";
    position: absolute;
    top: 0;
    left: 0;
    width: 2px;
    height: 0;
    background-color: rgba(1, 105, 1, 1);
    transition: 0.2s;
  }
  .outline-class ~ .focus-border i:after {
    left: auto;
    right: 0;
    top: auto;
    bottom: 0;
    transition-delay: 0.4s;
  }
  .outline-class:focus ~ .focus-border:before,
  .outline-class:focus ~ .focus-border:after {
    width: 100%;
    transition: 0.2s;
    transition-delay: 0.6s;
  }
  .outline-class:focus ~ .focus-border:after {
    transition-delay: 0.2s;
  }
  .outline-class:focus ~ .focus-border i:before,
  .outline-class:focus ~ .focus-border i:after {
    height: 100%;
    transition: 0.2s;
  }
  .outline-class:focus ~ .focus-border i:after {
    transition-delay: 0.4s;
  }

  #list::-webkit-scrollbar-track {
    -webkit-box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.3);
    background-color: rgba(122, 217, 153, 0.2);
  }

  #list::-webkit-scrollbar {
    width: 10px;
    background-color: #f5f5f5;
  }

  #list::-webkit-scrollbar-thumb {
    border-radius: 10px;
    background-image: -webkit-gradient(
      linear,
      left bottom,
      left top,
      color-stop(0.44, rgb(28, 148, 58)),
      color-stop(0.72, rgb(73, 189, 125)),
      color-stop(0.86, rgb(122, 217, 153))
    );
  }
</style>

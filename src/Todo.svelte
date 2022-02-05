<script>
  import { createEventDispatcher } from "svelte";

  let dispatch = createEventDispatcher();
  export let id;
  export let text;
  export let done;
  export let index;
  export let listLength;

  function notifyDone() {
    dispatch("notifyDone", id);
  }
  function notifyDelete() {
    dispatch("notifyDelete", id);
  }
  function notifyPositionUp() {
    dispatch("notifyPositionUp", id);
  }
  function notifyPositionDown() {
    dispatch("notifyPositionDown", id);
  }
</script>

<div id="task" class:done>
  <button id="check" class:checked={done} on:click={notifyDone}
    >{#if done}
      <i class="fas fa-check-circle" />
    {:else}
      <i class="far fa-circle" />
    {/if}</button
  >
  <p>{text}</p>
  <div class="position-div">
    <button
      on:click={notifyPositionUp}
      class:btn-active={index > 0}
      id="position-btn"><i class="fas fa-chevron-up" /></button
    >
    <button
      class:btn-active={index !== listLength - 1}
      on:click={notifyPositionDown}
      id="position-btn"><i class="fas fa-chevron-down" /></button
    >
  </div>
  <button class="del" on:click={notifyDelete}
    ><i class="fas fa-trash-alt" /></button
  >
</div>

<style>
  #task {
    display: flex;
    border: 1px solid #4d4c4c;
    min-height: 40px;
    background-color: rgb(225, 225, 225);
    margin-bottom: 8px;
    align-items: center;
    justify-content: space-between;
    position: relative;
    transition: border-color 0.3s, background-color 0.3s;
  }

  #task.done {
    border-color: rgba(1, 105, 1, 1);
    background-color: rgba(0, 255, 0, 0.15);
  }

  .position-div {
    margin: 0 8px 0 5px;
    display: flex;
    border: none;
    flex-direction: column;
  }

  #position-btn {
    padding: 0;
    margin: 0;
    height: 20px;
    width: 20px;
    color: transparent;
    transition: color 0.3s;
    border: none;
    background-color: transparent;
    cursor: pointer;
    pointer-events: none;
  }

  #position-btn.btn-active {
    pointer-events: all;
  }

  #task:hover #position-btn.btn-active {
    color: #414141;
  }

  #task:hover #position-btn.btn-active:hover {
    color: rgba(1, 105, 1, 1);
  }

  p {
    width: 100%;
    transition: text-decoration 0.3s, color 0.3s;
    color: #4d4c4c;
  }

  #task.done p {
    text-decoration: line-through;
    color: #808080;
  }

  .del {
    border: none;
    background-color: transparent;
    padding: 5px;
    border-radius: 10px;
    margin: 5px;
    transition: color 0.3s, opacity 0.3s;
    pointer-events: none;
    opacity: 0;
  }

  #task:hover .del {
    cursor: pointer;
    opacity: 1;
    pointer-events: all;
  }
  .del:hover {
    color: #b42e01;
  }

  #check {
    border: none;
    border-radius: 50%;
    padding: 0;
    color: #414141;
    margin: 10px;
    cursor: pointer;
    transition: color 0.3s;
    background-color: transparent;
  }
  #check:hover,
  #check:active {
    color: rgba(1, 105, 1, 1);
  }
  #check.checked {
    color: rgba(1, 105, 1, 1);
  }
</style>

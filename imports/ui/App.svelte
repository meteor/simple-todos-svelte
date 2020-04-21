<script>
  import { useTracker } from 'meteor/rdb:svelte-meteor-data';
  import Task from './Task.svelte';
  import { Tasks } from '../api/tasks.js'
  
  let newTask = "";

  $: tasks = useTracker(() => Tasks.find({}).fetch());

</script>
 
<div class="container">
  <header>
    <h1>Todo List</h1>
    <form class="new-task" on:submit|preventDefault={handleSubmit}>
      <input
        type="text"
        placeholder="Type to add new tasks"
        bind:value={newTask}
      />
    </form>
  </header>
  <ul>
  {#each $tasks as task}
    <Task
      key={task._id}
      task={task}
    />
  {/each}
  </ul>
</div>


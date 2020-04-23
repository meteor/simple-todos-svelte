<script>
  import { useTracker } from 'meteor/rdb:svelte-meteor-data';  
  import Task from './Task.svelte';
  import { Tasks } from '../api/tasks.js'
  
  let newTask = "";
  let hideCompleted = false;

  $: incompleteCount = useTracker(() => Tasks.find({ checked: { $ne: true } }).count()); 

  const taskStore = Tasks.find({}, { sort: { createdAt: -1 } });
  $: {
      tasks = $taskStore;
      if (hideCompleted) {
          tasks = tasks.filter(task => !task.checked);
      }
  };

 function handleSubmit(event) {
      Tasks.insert({
        text: newTask,
        createdAt: new Date() // current time
      });

      // Clear form
      newTask = "";
  };
</script>
 
<div class="container">
  <header>
    <h1>Todo List ({ $incompleteCount })</h1>
    <label className="hide-completed">
      <input
        type="checkbox"
        bind:checked={hideCompleted}
      />
      Hide Completed Tasks
    </label>
    <form class="new-task" on:submit|preventDefault={handleSubmit}>
      <input
        type="text"
        placeholder="Type to add new tasks"
        bind:value={newTask}
      />
    </form>
  </header>
  <ul>
  {#each tasks as task}
    <Task
      key={task._id}
      task={task}
    />
  {/each}
  </ul>
</div>


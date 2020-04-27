<script>
  import { Meteor } from "meteor/meteor";
  import { onMount } from 'svelte';
  import { useTracker } from 'meteor/rdb:svelte-meteor-data';  
  import { BlazeTemplate } from 'meteor/svelte:blaze-integration';
  import Task from './Task.svelte';
  import { Tasks } from '../api/tasks.js'

  let newTask = "";
  let hideCompleted = false;
  let tasks;
  let currentUser;
  
  onMount(async () => {
    Meteor.subscribe('tasks');
  });

  $: incompleteCount = useTracker(() => Tasks.find({ checked: { $ne: true } }).count()); 

  $: currentUser = useTracker(() => Meteor.user());

  const taskStore = Tasks.find({}, { sort: { createdAt: -1 } });
  $: {
      tasks = $taskStore;
      if (hideCompleted) {
          tasks = tasks.filter(task => !task.checked);
      }
  };

 function handleSubmit(event) {
      Meteor.call("tasks.insert", newTask);

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
    
    <BlazeTemplate template="loginButtons" />

    {#if $currentUser}
    <form class="new-task" on:submit|preventDefault={handleSubmit}>
      <input
        type="text"
        placeholder="Type to add new tasks"
        bind:value={newTask}
      />
    </form>
    {/if}
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


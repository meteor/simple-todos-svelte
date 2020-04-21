<script>
  import { useTracker } from 'meteor/rdb:svelte-meteor-data';  
  import { Tasks } from "../api/tasks.js";

  export let key;
  export let task;
  let showPrivateButton;

  $: currentUser = useTracker(() => Meteor.user());

  $: {
    showPrivateButton = false;
    if($currentUser){
      showPrivateButton = task.owner === $currentUser._id;
    }
  }

  function toggleChecked() {
      // Set the checked property to the opposite of its current value
      Meteor.call("tasks.setChecked", task._id, !task.checked);
  };

  function deleteThisTask() {
      Meteor.call("tasks.remove", task._id);
  };

  function togglePrivate() {
    Meteor.call("tasks.setPrivate", task._id, !task.private);
  }
  
</script>

<li class:checked="{task.checked}"
    class:private="{task.private}" >
  <button class="delete" on:click={deleteThisTask}>
    &times;
  </button>

  <input
    type="checkbox"
    readonly
    checked={!!task.checked}    
    on:click={toggleChecked}
  />

  {#if showPrivateButton}
    <button className="toggle-private" on:click="{togglePrivate}">
      { task.private ? "Private" : "Public" }
    </button>
  {/if}

  <span class="text">
    <strong>{ task.username }</strong>
    : { task.text }
  </span>
</li>

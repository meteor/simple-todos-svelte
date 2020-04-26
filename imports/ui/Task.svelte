<script>
  import { Tasks } from "../api/tasks.js";

  export let key;
  export let task;
  let showPrivateButton;

  function toggleChecked() {
      // Set the checked property to the opposite of its current value
      Meteor.call("tasks.setChecked", task._id, !task.checked);
  };

  function deleteThisTask() {
      Meteor.call("tasks.remove", task._id);
  };

</script>

<li class:checked="{task.checked}">
  <button class="delete" on:click={deleteThisTask}>
    &times;
  </button>

  <input
    type="checkbox"
    readonly
    checked={!!task.checked}    
    on:click={toggleChecked}
  />

  <span class="text">
    <strong>{ task.username }</strong>
    : { task.text }
  </span>
</li>

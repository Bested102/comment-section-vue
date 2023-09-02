<template>
  <div class="popUp">
    <h2>Delete comment</h2>
    <p>Are you sure you want to delete this comment? This will remove the comment and can't be undone.</p>
    <div class="choices">
      <span @click="$emit('cancel')">NO, CANCEL</span>
      <span class="confirmDelete">YES, DELETE</span>
    </div>
  </div>
</template>
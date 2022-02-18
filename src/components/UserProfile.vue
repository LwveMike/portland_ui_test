<template>
  <div class="user-profile">
    <img v-bind:src="user.avatar" alt="avatar" class="user-avatar">
    <div class="user-details">
      <p class="user-name">{{ user.username }}</p>
      <p class="user-id">{{ user.id }}</p>
    </div>
  </div>
</template>

<script>
export default {
  name: "UserProfile",
  props: {
    user: {
      type: Object,
    }
  }

}
</script>

<style scoped>
  .user-profile {
    width: 300px;
    border: 1px solid black;
    border-radius: 4px;
    display: flex;
    align-items: center;
    box-shadow: 0px 4px 20px 0px rgba(0,0,0,0.1);
    justify-content: space-between;
  }

  .user-avatar {
    display: block;
    width: 100px;
    height: 100px;
  }

  .user-details {
    display: flex;
    flex-direction: column;
    align-items: center;
    margin-right: 20px;
  }

  .user-name, .user-id {
    font-size: 20px;
  }
</style>
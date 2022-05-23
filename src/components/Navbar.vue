<template>
  <div class="navbar">
    <div @click="$router.push('/')">Vue 3</div>
    <div class="navbar-btns">
      <my-button @click="$router.push('/posts')"
        >Пости</my-button
      >
      <my-button
        style="margin-left: 20px"
        @click="$router.push('/about')"
        >Інформація</my-button
      >
    </div>
  </div>
</template>

<script>
import MyButton from "./UI/MyButton.vue";
export default {
  components: { MyButton },
};
</script>

<style scoped>
.navbar {
  height: 50px;
  background-color: teal;
  box-shadow: 2px 2px 4px gray;
  display: flex;
  align-items: center;
  padding: 0 15px;
}
.navbar-btns {
  margin-left: auto;
}
</style>

<router-link to="/posts">Пости</router-link>
<router-link to="/about">Інформація</router-link>

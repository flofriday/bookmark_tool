<template>
  <title>Bookmark Tool</title>
  <BookmarkTool />
</template>

<script>
import BookmarkTool from "./components/BookmarkTool.vue";

export default {
  name: "App",
  components: {
    BookmarkTool,
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: left;
  color: #2c3e50;
}
</style>

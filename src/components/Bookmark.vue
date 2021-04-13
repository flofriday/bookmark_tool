<template>
  <span>{{ bookmark.name }} </span>
  <a style="margin: 10px;" :href="bookmark.url"> {{ bookmark.url }} </a>
  <span> {{ new Date(1541622167 * 1000).toLocaleDateString() }}</span>
</template>

<script>
export default {
  name: "Bookmark",
  props: ["bookmark"],
  data() {
    return {};
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped></style>

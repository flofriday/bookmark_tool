<template>
  <input
    type="checkbox"
    id="scales"
    name="scales"
    @input="onToggle($event)"
    :checked="folder.selected"
  />
  <b>{{ folder.name }}</b>
  <ul>
    <li v-for="(bookmark, index) in folder.bookmarks" :key="index">
      <Bookmark :bookmark="bookmark"></Bookmark>
    </li>
  </ul>
</template>

<script>
import Bookmark from "./Bookmark.vue";

export default {
  name: "BookmarkFolder",
  props: ["folder", "index"],
  emits: ["selected"],
  components: {
    Bookmark,
  },
  data() {
    return {};
  },
  methods: {
    onToggle(event) {
      //console.log(this.folder);
      this.$emit("selected", this.index, event.target.checked);
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped></style>

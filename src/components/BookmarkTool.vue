<template>
  <h1>Bookmark Tool++</h1>
  <span>by <a href="https://github.com/flofriday">flofriday</a> </span>
  <div v-if="hasData">
    <ul>
      <li v-for="(folder, index) in folders" :key="index">
        <BookmarkFolder
          :folder="folder"
          :index="index"
          @selected="onSelected"
        ></BookmarkFolder>
      </li>
    </ul>
    <hr />
    <input type="checkbox" id="fakeTime" name="scales" checked />
    <label for="fakeTime">Fake Timestamps (set them to the current time)</label
    ><br />
    <button @click="onExport">Export</button>
  </div>
  <div v-else>
    <input
      type="file"
      ref="fileInput"
      accept="text/html"
      @change="onFilePicked"
    />
    <!--
    <div
      style="background-color: pink; text-align: center; padding:16px; border: 3px dashed grey"
      v-cloak
      @drop.prevent="onDrop"
      @dragover.prevent
    >
      Drop a exported bookmarks file here.
    </div>
    -->
  </div>
</template>

<script>
import BookmarkFolder from "./BookmarkFolder.vue";

export default {
  name: "BookmarkTool",
  components: {
    BookmarkFolder,
  },
  data() {
    return {
      hasData: false,
      folders: [],
    };
  },
  methods: {
    downloadFile(content, filename, contentType) {
      const a = document.createElement("a");
      const file = new Blob([content], { type: contentType });

      a.href = URL.createObjectURL(file);
      a.download = filename;
      a.click();

      URL.revokeObjectURL(a.href);
    },
    onExport() {
      let content = `
<!DOCTYPE NETSCAPE-Bookmark-file-1>
<!-- This is an automatically generated file.
     It will be read and overwritten.
     DO NOT EDIT! -->
<META HTTP-EQUIV="Content-Type" CONTENT="text/html; charset=UTF-8">
<TITLE>Bookmarks</TITLE>
<H1>Bookmarks Menu</H1>

<DL><p>`;

      let now = Math.floor(Date.now() / 1000);
      for (let folder of this.folders) {
        if (!folder.selected) continue;
        let lastDate = folder.lastModified;
        let addDate = folder.addDate;
        if (this.fakeTime) {
          lastDate = now;
          addDate = now;
        }
        content += `\n    <DT><H3 ADD_DATE="${addDate}" LAST_MODIFIED="${lastDate}">${folder.name}</H3>\n<DL><p>`;

        for (let bookmark of folder.bookmarks) {
          let lastDate = bookmark.lastModified;
          let addDate = bookmark.addDate;
          if (this.fakeTime) {
            lastDate = now;
            addDate = now;
          }
          content += `\n        <DT><A HREF="${bookmark.url}" ADD_DATE="${addDate}" LAST_MODIFIED="${lastDate}" ICON_URI="${bookmark.iconUri}">${bookmark.name}</A>`;
        }
        content += "\n    </DL><p>";
      }
      content += "\n</DL>";
      this.downloadFile(content, "history.html", "text/html");

      console.log(this.folders);
    },
    onSelected(index, value) {
      this.folders[index].selected = value;
    },
    onDrop(event) {
      event.preventDefault();
      console.log(event);
      console.log(event.dataTransfer.getData("text"));
    },
    async onFilePicked(event) {
      console.log(event.target.files);
      const file = event.target.files[0];

      let doc = document.createElement("html");
      doc.innerHTML = await file.text();
      let elements = Array.from(doc.children[1].children[1].children);
      elements.shift();

      for (let el of elements) {
        const header = el.children[0];
        let folder = {
          name: header.innerText,
          selected: true,
          addDate: header.attributes["add_date"].value,
          lastModified: header.attributes["last_modified"].value,
          bookmarks: [],
        };

        let links = Array.from(el.children[1].children);
        links.shift();
        for (let link of links) {
          const ancor = link.children[0];
          const bookmark = {
            name: ancor.innerText,
            url: ancor.attributes["href"].value,
            addDate: ancor.attributes["add_date"].value,
            lastModified: ancor.attributes["last_modified"].value,
            iconUri:
              ancor.attributes["icon_uri"] == undefined
                ? null
                : ancor.attributes["icon_uri"].value,
            icon:
              ancor.attributes["icon"] == undefined
                ? null
                : ancor.attributes["icon"].value,
          };
          folder.bookmarks.push(bookmark);
        }

        this.folders.push(folder);
      }

      this.hasData = true;
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped></style>

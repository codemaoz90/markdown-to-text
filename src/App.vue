<template>
  <div id="notebook">
    <aside class="side-bar">
      <!-- Toolbar -->
      <div class="toolbar">
        <!-- Add note button -->
        <button :title="addButtonTitle" @click="addNote">
          <i class="material-icons">add</i> Add note
        </button>
      </div>
      <!-- Here will be the note list -->

      <div class="notes">
        <div
          class="note"
          v-for="(note, index) of notes"
          @click="selectNote(note)"
          :key="index"
        >
          {{ note.title }}
        </div>
      </div>
    </aside>
    <!-- Main pane -->
    <section class="main">
      <textarea v-model="content"></textarea>
    </section>

    <!-- Preview pane -->
    <aside class="preview" v-html="notePreview"></aside>
  </div>
</template>

<script>
import "material-icons/iconfont/material-icons.css"
import { marked } from "marked"
export default {
  name: "App",
  components: {
    // HelloWorld,
  },
  created() {
    this.content =
      localStorage.getItem("content") || "You can write in **Markdown**"
  },
  data() {
    return {
      content: "This is a note. **marked**",
      notes: [],
      selectedId: null
    }
  },
  computed: {
    notePreview() {
      // Markdown rendered to HTML
      return marked(this.content)
    },
    addButtonTitle() {
      return this.notes.length + " note(s) already"
    }
  },
  methods: {
    saveNote(val) {
      console.log("saving note", val)
      localStorage.setItem("content", val)
    },
    addNote() {
      const time = Date.now()
      // Default new note
      const note = {
        id: String(time),
        title: "New note " + (this.notes.length + 1),
        content:
          "**Hi!** This notebook is using[markdown](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) for formatting!",
        created: time,
        favorite: false
      }
      // Add to the list
      this.notes.push(note)
    },
    selectNote(note) {
      this.selectedId = note.id
      console.log(this.selectedId)
    }
  },
  watch: {
    // Watching the "content" data property!
    content: "saveNote"
  }
}
</script>

<style src="@/assets/styles.css"></style>

<style lang="scss">
#notebook {
  height: 100vh;

  .toolbar {
    text-align: center;
  }
}
</style>

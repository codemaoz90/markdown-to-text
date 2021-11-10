<template>
  <div id="notebook">
    <!-- Sidebar -->
    <aside class="side-bar">
      <div class="toolbar">
        <button @click="addNote" :title="notes.length + ' note(s) already'">
          <i class="material-icons">add</i> Add note
        </button>
      </div>
      <div class="notes">
        <div
          class="note"
          v-for="(note, index) of sortedNotes"
          :key="index"
          :class="{ selected: note === selectedNote }"
          @click="selectNote(note)"
        >
          <i class="icon material-icons" v-if="note.favorite">star</i
          >{{ note.title }}
        </div>
      </div>
    </aside>

    <template v-if="selectedNote">
      <!-- Main pane -->
      <section class="main">
        <div class="toolbar">
          <input v-model="selectedNote.title" placeholder="Note title" />

          <button @click="favoriteNote" title="Favorite note">
            <i class="material-icons">{{
              selectedNote.favorite ? "star" : "star_border"
            }}</i>
          </button>

          <button @click="removeNote" title="Remove note">
            <i class="material-icons">delete</i>
          </button>
        </div>
        <textarea v-model="selectedNote.content"></textarea>
        <div class="toolbar status-bar">
          <span class="date">
            <span class="label">Created</span>
            <span class="value">{{ selectedNote.created | date }}</span>
          </span>
          <span class="lines">
            <span class="label">Lines</span>
            <span class="value">{{ linesCount }}</span>
          </span>
          <span class="words">
            <span class="label">Words</span>
            <span class="value">{{ wordsCount }}</span>
          </span>
          <span class="characters">
            <span class="label">Characters</span>
            <span class="value">{{ charactersCount }}</span>
          </span>
        </div>
      </section>

      <!-- Preview pane -->
      <aside class="preview" v-html="notePreview"></aside>
    </template>
  </div>
</template>

<script>
/* eslint-disable vue/return-in-computed-property */
import "material-icons/iconfont/material-icons.css"
import { marked } from "marked"
export default {
  name: "App",
  components: {
    // HelloWorld,
  },

  data() {
    return {
      // These are loaded from localStorage and have a default value
      // Don't forget the JSON parsing for the notes array
      notes: JSON.parse(localStorage.getItem("notes")) || [],
      selectedId: localStorage.getItem("selected-id") || null
    }
  },

  // Computed properties
  computed: {
    selectedNote() {
      // We return the matching note with selectedId
      return this.notes.find((note) => note.id === this.selectedId)
    },

    notePreview() {
      // Markdown rendered to HTML
      return this.selectedNote ? marked(this.selectedNote.content) : ""
    },

    sortedNotes() {
      return this.notes
        .slice()
        .sort((a, b) => a.created - b.created)
        .sort((a, b) => (a.favorite === b.favorite ? 0 : a.favorite ? -1 : 1))
    },

    linesCount() {
      if (this.selectedNote) {
        // Count the number of new line characters
        return this.selectedNote.content.split(/\r\n|\r|\n/).length
      }
    },

    wordsCount() {
      if (this.selectedNote) {
        var s = this.selectedNote.content
        // Turn new line cahracters into white-spaces
        s = s.replace(/\n/g, " ")
        // Exclude start and end white-spaces
        s = s.replace(/(^\s*)|(\s*$)/gi, "")
        // Turn 2 or more duplicate white-spaces into 1
        s = s.replace(/[ ]{2,}/gi, " ")
        // Return the number of spaces
        return s.split(" ").length
      }
    },

    charactersCount() {
      if (this.selectedNote) {
        return this.selectedNote.content.split("").length
      }
    }
  },

  // Change watchers
  watch: {
    // When our notes change, we save them
    notes: {
      // The method name
      handler: "saveNotes",
      // We need this to watch each note's properties inside the array
      deep: true
    },
    // Let's save the selection too
    selectedId(val) {
      localStorage.setItem("selected-id", val)
    }
  },

  methods: {
    // Add a note with some default content and select it
    addNote() {
      const time = Date.now()
      // Default new note
      const note = {
        id: String(time),
        title: "New note " + (this.notes.length + 1),
        content:
          "**Hi!** This notebook is using [markdown](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) for formatting!",
        created: time,
        favorite: false
      }
      // Add
      this.notes.push(note)
      // Select
      this.selectNote(note)
    },

    // Remove the selected note and select the next one
    removeNote() {
      if (this.selectedNote && confirm("Delete the note?")) {
        // Remove the note in the notes array
        const index = this.notes.indexOf(this.selectedNote)
        if (index !== -1) {
          this.notes.splice(index, 1)
        }
      }
    },

    selectNote(note) {
      // This will update the 'selectedNote' computed property
      this.selectedId = note.id
    },

    saveNotes() {
      // Don't forget to stringify to JSON before storing
      localStorage.setItem("notes", JSON.stringify(this.notes))
      console.log("Notes saved!", new Date())
    },

    favoriteNote() {
      // this.selectedNote.favorite = !this.selectedNote.favorite
      // this.selectedNote.favorite = this.selectedNote.favorite ^ true
      this.selectedNote.favorite ^= true
    }
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

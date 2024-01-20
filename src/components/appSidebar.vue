<template>
  <div class="app-sidebar">
    <!-- サイドバーの内容 -->
    <div class="app-sidebar-header">
        <h1>ノート</h1>
        <button @click="addNewNote">追加</button>
    </div>
    <div class="app-sidebar-note"
      v-for="note in sortedNotes"
      :key="note.id"
      :class=" {'app-sidebar-note-selected': note.id === selectedNote} "
      @click="selectNoteHandler(note.id)"
    >
        <div class="app-sidebar-title">
            <strong>{{ note.title }}</strong>
            <button @click="() => deleteNoteHandler(note.id)">削除</button>
        </div>
        <p>{{ note.content }}</p>
        <small>{{ formatDate(note.modDate) }}</small>
    </div>
  </div>
</template>

<script>
export default {
  props: ['notes', 'addNote', 'deleteNote', 'selectNote'],
  computed: {
    sortedNotes() {
      return this.notes.slice().sort((a, b) => {
        return new Date(b.modDate) - new Date(a.modDate);
      });
    }
  },
  methods: {
    addNewNote() {
      this.addNote();
    },
    deleteNoteHandler(id) {
      this.deleteNote(id);
    },
    selectNoteHandler(id) {
      this.selectNote(id);
    },
    formatDate(date) {
      return date ? new Date(date).toLocaleDateString('ja-JP', { year: 'numeric', month: '2-digit', day: '2-digit' }) : '';
    }
  }
}
</script>

<style scoped>
    /* サイドバーに関するスタイルをここに追加 */
  .app-sidebar {
    width: 200px;
    background-color: #f0f0f0;
    padding: 20px;
    box-sizing: border-box;
  }

  .app-sidebar-header {
    margin-bottom: 20px;
    display: flex;
    align-items: center;
    justify-content: space-between;
  }

  .app-sidebar-title {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 10px;
  }

  .app-sidebar-note {
    border: 1px solid #ccc;
    padding: 10px;
    margin-bottom: 10px;
  }

  button {
    cursor:pointer;
    padding: 5px 10px;
    background-color: #4caf50;
    color: white;
    border: none;
    border-radius: 3px;
  }

  button:hover {
    background-color: #45a049;
  }

  .app-sidebar-note-selected {
    background-color: #e0f7fa; /* ハイライト色 */
  }
</style>

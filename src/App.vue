<template>
  <div class="App">
    <Sidebar :notes="notes"
    :addNote="addNote"
    :deleteNote="deleteNote"
    :selectNote="selectNote"/>
    <Main :notes="notes" :selectedNote="selectedNote" :onUpdateNote="onUpdateNote"/>
  </div>
</template>

<script>
import Sidebar from '@/components/appSidebar.vue';
import Main from '@/components/appMain.vue';
import { v4 as uuid4 } from 'uuid';

export default {
  components: {
    Sidebar,
    Main,
  },
  data() {
    return {
      LOCAL_STORAGE_KEY: 'my_notes',
      notes: [],
      selectedNote: null
    };
  },
  methods: {
    getStoredNotes() {
      const storedNotes = localStorage.getItem(this.LOCAL_STORAGE_KEY);
      return storedNotes ? JSON.parse(storedNotes): [];
    },
    saveNotesToLocalStorage() {
      localStorage.setItem(this.LOCAL_STORAGE_KEY, JSON.stringify(this.notes));
    },
    addNote() {
      const newNote = {
        id: uuid4(),
        title: '新しいノート',
        content: 'ノートの内容',
        modDate: new Date(),
      };
      this.notes = [...this.notes, newNote];
    },
    deleteNote(id) {
      const index = this.notes.findIndex((note) => note.id === id);
      if (index !== -1) {
        this.notes.splice(index, 1);
        this.removeFromLocalStorage(id);
      }
    },
    selectNote(id) {
      this.selectedNote = id;
    },
    removeFromLocalStorage(id) {
      const storedNotes = JSON.parse(localStorage.getItem(this.LOCAL_STORAGE_KEY)) || [];
      const updatedNotes = storedNotes.filter((note) => note.id !==id);
      localStorage.setItem(this.LOCAL_STORAGE_KEY, JSON.stringify(updatedNotes));
    }
  },
  watch: {
    notes: {
      handler() {
        this.saveNotesToLocalStorage();
      },
      deep: true,
    },
  },
  created() {
    this.notes = this.getStoredNotes();
  },
  beforeDestory() {
    this.saveNotesToLocalStorage();
  },
};
</script>

<style>
   /* グローバルなスタイルをここに追加 */
  .App {
    display: flex;
  }
</style>

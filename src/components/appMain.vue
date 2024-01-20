<template>
  <div class="app-main">
    <!-- メインエリアの内容をここに追加 -->
    <div class="app-main-note-edit">
      <input id="title" type="text" v-model="getSelectedNote.title">
      <textarea name="" id="content" cols="30" rows="10" v-model="getSelectedNote.content" placeholder="ノート内容を記入"></textarea>
    </div>
    <div class="app-main-note-preview">
      <h1 class="preview-title">{{ getSelectedNote.title }}</h1>
      <!-- vue-markdownコンポーネントを使ってMarkdownを表示 -->
      <Markdown :source="getSelectedNote.content" class="markdown-preview" />
    </div>
  </div>
</template>

<script>
import Markdown from 'vue3-markdown-it';

export default {
  components: {
     Markdown,
  },
  props: ['notes','selectedNote'],
  data() {
    return {
      selectedNoteId: this.selectedNote || null,
    };
  },
  computed: {
    getSelectedNote() {
      const note = this.notes.find((note) => note.id === this.selectedNoteId);
      const currentDate = new Date();
      if(!note) {
        return {title: '', content: '', modDate: currentDate}; //該当するノートが見つからない場合
      }
      return note;
    },
  },
  watch: {
    selectedNote(newSelectedNote) {
      this.selectedNoteId = newSelectedNote;
      this.saveNotesToLocalStorage();
    },
    'getSelectedNote.title'() {
      this.saveNotesToLocalStorage();
    },
    'getSelectedNote.content'() {
      this.saveNotesToLocalStorage();
    },
  },
  methods: {
    saveNotesToLocalStorage() {
      localStorage.setItem('my_notes', JSON.stringify(this.notes));
    },
  },
};
</script>


<style scoped>
    /* メインエリアに関するスタイルをここに追加 */
  .app-main {
    flex: 1;
  }

 .app-main-note-edit,
.app-main-note-preview{
    height: 50vh;
}

.app-main-note-edit {
    padding: 25px;
}

.app-main-note-edit input,
textarea{
    display: block;
    border: 1px solid #ddd;
    margin-bottom: 20px;
    width: 100%;
    height: calc(50vh -130px);
    padding: 5px;
    resize: none;
    font-size: 16px;
}

.app-main-note-edit input {
    height: 50px;
    font-size: 2rem;
}

.app-main-note-preview {
    border-top: 1px solid #ddd;
    overflow-y: scroll;
    background-color: rgba(0, 0, 0, 0.04);
}

.preview-title {
    padding: 25px 25px 0 25px;
    margin: 0;
}

.markdown-preview {
    padding: 0 25px 25px 25px;
    line-height: 2rem;
}

.no-active-note {
    width: 70%;
    height: 100vh;
    line-height: 100vh;
    text-align: center;
    font-size: 2rem;
    color: #999;
}
</style>

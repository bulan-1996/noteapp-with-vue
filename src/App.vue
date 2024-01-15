<script setup>
import Sidebar from '@/components/appSidebar.vue';
import Main from '@/components/appMain.vue';
import { ref } from "vue";
import { v4 as uuid4 } from 'uuid';
import { onMounted, onBeforeUnmount, watch} from 'vue';

const LOCAL_STORAGE_KEY = 'my_notes';  // ローカルストレージのキーを定義

//ローカルストレージからデータを取得する関数
const getStoredNotes = () => {
  const storedNotes = localStorage.getItem(LOCAL_STORAGE_KEY);
  return storedNotes ? JSON.parse(storedNotes) : [];
}

const notes = ref(getStoredNotes()); // ノートデータの初期化
const selectedNote = ref(notes.value[0].id);
// watchを使用してnotesが変更されたらローカルストレージに保存
watch(() => notes.value, () => {
  saveNotesToLocalStorage();
});

const addNote = () => {
  const newNote = {
    id: uuid4(),
    title: '新しいノート',
    content: 'ノートの内容',
    modDate: new Date(), //作成日を追加
  };
  notes.value = [...notes.value, newNote];
  // notes.value.push(newNote);
}

const deleteNote = (id) => {
  // idに一致するノートを検索し、削除
  const index = notes.value.findIndex((note) => note.id === id);
  if(index !== -1) {
    notes.value.splice(index, 1);

    // localStorageからも削除
    removeFromLocalStorage(id);
  }
}

const selectNote = (id) => {
  selectedNote.value = id;
};

// 削除コマンド
const removeFromLocalStorage = (id) => {
  const storedNotes = JSON.parse(localStorage.getItem(LOCAL_STORAGE_KEY)) || [];
  const updatedNotes = storedNotes.filter((note) => note.id !== id);
  localStorage.setItem(LOCAL_STORAGE_KEY, JSON.stringify(updatedNotes));
};

// ローカルストレージにデータを保存する関数
const saveNotesToLocalStorage = () => {
  localStorage.setItem(LOCAL_STORAGE_KEY, JSON.stringify(notes.value));
};

// ページが読み込まれたときにローカルストレージからデータを取得
onMounted(() => {
  notes.value = getStoredNotes();
});

// ページが閉じられる前にデータを保存
onBeforeUnmount(() => {
  saveNotesToLocalStorage();
});
</script>

<template>
  <div class="App">
    <Sidebar :notes="notes"
    :addNote="addNote"
    :deleteNote="deleteNote"
    :selectNote="selectNote"/>
    <Main :notes="notes" :selectedNote="selectedNote" :onUpdateNote="onUpdateNote"/>
  </div>
</template>
<style>
   /* グローバルなスタイルをここに追加 */
  .App {
    display: flex;
  }
</style>

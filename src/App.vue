<template>
  <div class="container">
    <form @submit.prevent="tambah" class="form">
      <h2>{{ form.id ? 'Edit Artikel' : 'Tambah Artikel Baru' }}</h2>
      <input type="text" v-model="form.title" placeholder="Judul" /><br />
      <textarea v-model="form.content" placeholder="Konten"></textarea><br />
      <div class="buttons">
        <button type="submit" :class="form.id ? 'btn simpan' : 'btn tambah'">
          {{ form.id ? 'Simpan' : 'Tambah' }}
        </button>
        <button type="button" @click="resetForm" class="btn batal">Batal</button>
      </div>
    </form>

    <div class="articles">
      <h2>Artikel</h2>
      <ul>
        <li v-for="artikel in artikels" :key="artikel.id" class="article">
          <div class="article-content">
            <h3>{{ artikel.title }}</h3>
            <p>{{ artikel.content }}</p>
          </div>
          <div class="article-actions">
            <button @click="edit(artikel)" class="btn edit">Edit</button>
            <button @click="hapus(artikel.id)" class="btn hapus">Hapus</button>
          </div>
        </li>
      </ul>
      <button @click="muatUlang" class="btn reload">Muat Ulang Artikel</button>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, reactive } from 'vue';
import axios from 'axios';

const form = reactive({
  id: null,
  title: '',
  content: ''
});
const artikels = ref([]);

async function muatUlang() {
  try {
    const response = await axios.get('http://localhost:3000/articles');
    artikels.value = response.data;
  } catch (error) {
    console.error('Error memuat artikel:', error);
  }
}

async function tambah() {
  console.log("Fungsi tambah dipanggil");
  console.log("Data yang akan dikirim:", form.title, form.content);

  if (form.id) {
    try {
      const response = await axios.put(`http://localhost:3000/articles/${form.id}`, {
        title: form.title,
        content: form.content
      });

      console.log("Respons dari server:", response.data);
      artikels.value = artikels.value.map((artikel) =>
        artikel.id === response.data.id ? response.data : artikel
      );
      resetForm();
    } catch (error) {
      console.error('Error mengupdate artikel:', error);
      alert('Gagal mengupdate artikel. Periksa konsol untuk detail.');
    }
  } else {
    try {
      const response = await axios.post('http://localhost:3000/articles', {
        title: form.title,
        content: form.content
      });

      console.log("Respons dari server:", response.data);
      artikels.value.push(response.data);
      resetForm();
    } catch (error) {
      console.error('Error menambah artikel:', error);
      alert('Gagal menambah artikel. Periksa konsol untuk detail.');
    }
  }
}

function edit(artikel) {
  form.id = artikel.id;
  form.title = artikel.title;
  form.content = artikel.content;
}

function resetForm() {
  form.id = null;
  form.title = '';
  form.content = '';
}

async function hapus(id) {
  try {
    await axios.delete(`http://localhost:3000/articles/${id}`);
    artikels.value = artikels.value.filter(artikel => artikel.id !== id);
  } catch (error) {
    console.error('Error menghapus artikel:', error);
    alert('Gagal menghapus artikel. Periksa konsol untuk detail.');
  }
}

onMounted(muatUlang);
</script>

<style>
/* Global styles */
body {
  font-family: 'Arial', sans-serif;
  background: #1e1e1e;
  color: #eee;
  margin: 0;
  padding: 100px;
  background-image: url(./assets/1.jpg);
  background-repeat: no-repeat ;
  background-size: cover;
}

/* Container styles */
.container {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
  background: #141414;
  border-radius: 10px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
}

/* Form styles */
.form {
  margin-bottom: 20px;
}

.form h2 {
  margin-bottom: 20px;
  font-size: 24px;
  color: #d30fff;
}

.form input,
.form textarea {
  width: 100%;
  padding: 10px;
  margin-bottom: 10px;
  border: 1px solid #555;
  border-radius: 5px;
  box-sizing: border-box;
  background: #444;
  color: #eee;
}

.form .buttons {
  display: flex;
  gap: 10px;
}

.btn {
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  color: #fff;
  cursor: pointer;
  transition: background 0.3s, transform 0.2s;
}

.btn.tambah {
  background: #d30fff;
}

.btn.tambah:hover {
  background: #00ffbf;
  transform: scale(1.05);
}

.btn.simpan {
  background: #2196f3;
}

.btn.simpan:hover {
  background: #1e88e5;
  transform: scale(1.05);
}

.btn.batal {
  background: #d30fff;
}

.btn.batal:hover {
  background: #e53935;
  transform: scale(1.05);
}

/* Articles styles */
.articles {
  margin-top: 20px;
}

.articles h2 {
  margin-bottom: 20px;
  font-size: 24px;
  color: #d30fff;
}

.article {
  display: flex;
  justify-content: space-between;
  align-items: center;
  border: 1px solid #555;
  padding: 15px;
  margin-bottom: 10px;
  border-radius: 5px;
  background: #333;
}

.article-content h3 {
  margin: 0 0 10px;
  font-size: 20px;
  color: #d30fff;
}

.article-content p {
  margin: 0;
}

.article-actions {
  display: flex;
  gap: 10px;
}

.btn.edit {
  background: #d30fff;
}

.btn.edit:hover {
  background: #1e88e5;
  transform: scale(1.05);
}

.btn.hapus {
  background: #d30fff;
}

.btn.hapus:hover {
  background: #e53935;
  transform: scale(1.05);
}

.btn.reload {
  background: #d30fff;
  width: 100%;
  margin-top: 20px;
}

.btn.reload:hover {
  background: #e53935;
  transform: scale(1.05);
}
</style>

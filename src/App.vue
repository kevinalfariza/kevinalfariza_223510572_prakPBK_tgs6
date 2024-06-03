<script setup>
import { ref, onMounted, reactive } from 'vue';
import axios from 'axios';

const form = reactive({
  id: null,
  title: '',
  content: '',
});

const articles = ref([]);

async function load() {
  try {
    const response = await axios.get('http://localhost:3000/articles');
    articles.value = response.data;
  } catch (error) {
    console.error('Error loading articles:', error);
  }
}

async function save() {
  try {
    const url = form.id
      ? `http://localhost:3000/articles/${form.id}`
      : 'http://localhost:3000/articles';
    const method = form.id ? 'put' : 'post';
    const response = await axios[method](url, form);

    if (form.id) {
      articles.value = articles.value.map((article) =>
        article.id === response.data.id ? response.data : article
      );
    } else {
      articles.value.push(response.data);
    }

    form.id = null;
    form.title = '';
    form.content = '';
  } catch (error) {
    console.error('Error saving article:', error);
  }
}

async function deleteArticle(id) {
  try {
    await axios.delete(`http://localhost:3000/articles/${id}`);
    articles.value = articles.value.filter((article) => article.id !== id);
  } catch (error) {
    console.error('Error deleting article:', error);
  }
}

function edit(article) {
  form.id = article.id;
  form.title = article.title;
  form.content = article.content;
}

onMounted(load);
</script>

<template>
  <div class="container">
    <form @submit.prevent="save" class="form">
      <input type="text" v-model="form.title" placeholder="Title" /><br />
      <textarea v-model="form.content" placeholder="Content"></textarea><br />
      <button type="submit">Save</button>
    </form>
    <ul class="articles">
      <li v-for="article in articles" :key="article.id" class="article-item">
        <h3>{{ article.title }}</h3>
        <p>{{ article.content }}</p>
        <button @click="edit(article)" class="edit-btn">Edit</button>
        <button @click="deleteArticle(article.id)" class="delete-btn">Delete</button>
      </li>
    </ul>
    <button @click="load" class="load-btn">Load</button>
  </div>
</template>

<style>
.container {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
  font-family: Arial, sans-serif;
  background: #f0f0f0;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.2);
  border-radius: 10px;
}

.form {
  margin-bottom: 20px;
  background: #ffffff;
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.form input[type="text"],
.form textarea {
  width: 100%;
  padding: 12px;
  margin-bottom: 10px;
  border: 1px solid #ddd;
  border-radius: 6px;
  box-sizing: border-box;
  font-size: 16px;
}

.form button {
  padding: 12px 20px;
  border: none;
  background-color: #007bff;
  color: white;
  border-radius: 6px;
  cursor: pointer;
  font-size: 16px;
}

.form button:hover {
  background-color: #0056b3;
}

.articles {
  list-style: none;
  padding: 0;
}

.article-item {
  background: white;
  margin-bottom: 10px;
  padding: 20px;
  border: 1px solid #ddd;
  border-radius: 10px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.article-item h3 {
  margin: 0 0 10px;
  font-size: 20px;
}

.article-item p {
  margin: 0 0 10px;
  font-size: 16px;
  line-height: 1.6;
}

.edit-btn,
.delete-btn {
  padding: 8px 12px;
  border: none;
  color: white;
  border-radius: 6px;
  cursor: pointer;
  font-size: 14px;
}

.edit-btn {
  background-color: #28a745;
}

.edit-btn:hover {
  background-color: #218838;
}

.delete-btn {
  background-color: #dc3545;
}

.delete-btn:hover {
  background-color: #c82333;
}

.load-btn {
  padding: 12px 20px;
  border: none;
  background-color: #17a2b8;
  color: white;
  border-radius: 6px;
  cursor: pointer;
  display: block;
  margin: 20px 0 0;
  text-align: center;
  font-size: 16px;
}

.load-btn:hover {
  background-color: #138496;
}
</style>

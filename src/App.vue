<template>
  <div>
    <form @submit.prevent="save">
      <input type="text" v-model="form.title" placeholder="Title" /><br>
      <textarea v-model="form.content" placeholder="Content"></textarea><br>
      <button type="submit">Save</button>
    </form>
    <ul>
      <li v-for="article in articles" :key="article.id">
        <h3>{{ article.title }}</h3>
        <p>{{ article.content }}</p>
        <button @click="edit(article)">Edit</button>
        <button @click="remove(article.id)">Delete</button>
      </li>
    </ul>
    <button @click="load">Load</button>
    <p style="padding:10px" class="text-h6">Welcome to Data Buku View</p>
  </div>
</template>

<script>
import { ref, reactive, onMounted } from 'vue';
import axios from 'axios';

export default {
  setup() {
    const form = reactive({
      id: null,
      title: '',
      content: ''
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
        const url = form.id ? `http://localhost:3000/articles/${form.id}` : 'http://localhost:3000/articles';
        const method = form.id ? "put" : "post";
        const response = await axios[method](url, form);

        if (method === 'put') {
          const index = articles.value.findIndex(article => article.id === response.data.id);
          if (index !== -1) {
            articles.value[index] = response.data;
          }
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

    async function remove(id) {
      try {
        await axios.delete(`http://localhost:3000/articles/${id}`);
        articles.value = articles.value.filter(article => article.id !== id);
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

    return { form, articles, save, edit, remove, load };
  }
}
</script>

<style scoped>
body {
  font-family: 'Arial, sans-serif';
  background-color: #50ff76;
  color: #333;
  margin: 0;
  padding: 20px;
}

h3 {
  margin: 0;
  color: #333;
  font-size: 24px; /* Change the font size */
}

/* Form Styling */
form {
  background-color: #fff;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  margin-bottom: 20px;
}

input[type="text"],
textarea {
  width: 100%;
  padding: 10px;
  margin-bottom: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 18px; /* Change the font size */
}

button[type="submit"] {
  background-color: #e4ffe5;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 4px;
  cursor: pointer;
  font-size: 18px; /* Change the font size */
  transition: background-color 0.3s ease;
}

/* Article List Styling */
ul {
  list-style-type: none;
  padding: 0;
}

li {
  background-color: #fff;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  margin-bottom: 10px;
}

li h3 {
  margin-bottom: 10px;
  font-size: 20px; /* Change the font size */
}

li p {
  margin-bottom: 10px;
  font-size: 16px; /* Change the font size */
}

/* Button Styling */
button {
  background-color: #92006e;
  color: white;
  border: none;
  padding: 10px 15px;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px; /* Change the font size */
  margin-right: 10px;
  transition: background-color 0.3s ease;
}

button:last-child {
  background-color: #4d0404;
}

/* Welcome Text Styling */
.text-h6 {
  font-size: 24px; /* Change the font size */
  color: #333;
  margin-top: 20px;
  text-align: center;
}
</style>

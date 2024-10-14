<template>
  <div>
    <h1>Autores</h1>
    
    <form @submit.prevent="handleSubmit">
      <input v-model="autor.nome" placeholder="Nome" required />
      <input v-model="autor.email" placeholder="Email" type="email" />
      <button type="submit">{{ isEditing ? 'Atualizar' : 'Adicionar' }}</button>
    </form>

    <ul>
      <li v-for="autor in autores" :key="autor.id">
        {{ autor.nome }} - {{ autor.email }}
        <button @click="editAutor(autor)">Editar</button>
        <button @click="deleteAutor(autor.id)">Excluir</button>
      </li>
    </ul>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      autores: [],
      autor: {
        nome: '',
        email: ''
      },
      isEditing: false,
      currentAutorId: null
    };
  },
  methods: {
    async fetchAutores() {
      const response = await axios.get('/api/autores/');
      this.autores = response.data;
    },
    async handleSubmit() {
      if (this.isEditing) {
        await axios.put(`/api/autores/${this.currentAutorId}/`, this.autor);
      } else {
        await axios.post('/api/autores/', this.autor);
      }
      this.resetForm();
      await this.fetchAutores();
    },
    editAutor(autor) {
      this.autor = { ...autor };
      this.isEditing = true;
      this.currentAutorId = autor.id;
    },
    async deleteAutor(id) {
      await axios.delete(`/api/autores/${id}/`);
      await this.fetchAutores();
    },
    resetForm() {
      this.autor = { nome: '', email: '' };
      this.isEditing = false;
      this.currentAutorId = null;
    }
  },
  mounted() {
    this.fetchAutores();
  }
};
</script>

<style scoped>
/* Adicione estilos personalizados aqui */
</style>

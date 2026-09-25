<script setup>
import { onMounted, ref } from 'vue';

const API_URL = 'http://localhost:3000/pets';

const pets = ref([]);
const tutores = ref([]);

  async function carregador() {
    const respostaPets = await fetch(API_URL + "/pets");
    pets.value = await repostaPets.json();

    const respostasTutores = await fetch(API_URL + "/tutores");
    tutores.value = await respostasTutores.json();
    
    onmunted(carregar)
  }

</script>

<template>
  <div>
    <header class="mb-4">
      <h1 class="text-2xl font-bold">Listagem de Pets</h1>
      <p class="text-body-secondary mb-0">
        Listagem dos Pets cadastrados no sistema.
      </p>
    </header>
  </div>

<table> 
  <thead>
    <tr>
      <th>Nome</th>
      <th>Idade</th>
      <th>Raça</th>
      <th>Tutor</th>
    </tr>
</table>
<tbody>
  <tr v-for="pet in pets" :key="pet.id">
    <td>{{ pet.id }}</td>
    <td>{{ pet.nome }}</td>
    <td>{{ pet.especial }}</td>
    <td>{{ 
      tutores.find(tutor => tutor.id === pet.tutorId)?.nome ||
       "não encontrado" 
    }}</td>
  </tr>
  
</template>
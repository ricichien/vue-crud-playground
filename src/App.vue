<template>
  <div>
    <h1><span class="gradient-text">Projeto Vue com Firestore</span></h1>

    <form @submit.prevent="addData">
      <label for="nome">Nome:</label>
      <input v-model="newData.nome" type="text" id="nome" required>

      <label for="idade">Idade:</label>
      <input v-model="newData.idade" type="number" id="idade" required>

      <button type="submit">{{ editingMode ? 'Atualizar' : 'Adicionar' }}</button>
      <button type="button" @click="cancelEdit" v-if="editingMode">Cancelar</button>
    </form>

    <table>
      <thead>
        <tr>
          <th>Nome</th>
          <th>Idade</th>
          <th>Ações</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="item in data" :key="item.id">
          <td>{{ item.nome }}</td>
          <td>{{ item.idade }}</td>
          <td class="action-column">
            <button class="table-button" @click="editData(item)">Editar</button>
            <button class="table-button" @click="deleteData(item.id)">Apagar</button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import { getFirestore, collection, getDocs, addDoc, deleteDoc, doc, setDoc } from 'firebase/firestore';
import { initializeApp } from 'firebase/app';

const firebaseConfig = {
  apiKey: "AIzaSyBo_6_wHm-NPPJzKyOfTzsqSZVY2BcIanM",
  authDomain: "projeto-crud-68788.firebaseapp.com",
  projectId: "projeto-crud-68788",
  storageBucket: "projeto-crud-68788.appspot.com",
  messagingSenderId: "666833179421",
  appId: "1:666833179421:web:c87dd0783eab96c75b87f4",
  measurementId: "G-9ZNQWSQFFC"
};

const firebaseApp = initializeApp(firebaseConfig);
const db = getFirestore(firebaseApp);
const minhaColecao = collection(db, 'minhaColecao');

const newData = ref({
  nome: '',
  idade: 0,
});

const data = ref([]);

let editingMode = ref(false);

const readData = async () => {
  const querySnapshot = await getDocs(minhaColecao);
  data.value = querySnapshot.docs.map((doc) => ({ id: doc.id, ...doc.data() }));
};

const updateData = async (id, newData) => {
  await setDoc(doc(minhaColecao, id), newData);
};

const deleteData = async (id) => {
  await deleteDoc(doc(minhaColecao, id));
  readData();
};

const editData = (item) => {
  newData.value = { ...item };
  editingMode.value = true;
};

const cancelEdit = () => {
  editingMode.value = false;
  newData.value = { nome: '', idade: 0 };
};

const addData = async () => {
  if (editingMode.value) {
    await updateData(newData.value.id, newData.value);
    editingMode.value = false;
  } else {
    await addDoc(minhaColecao, newData.value);
  }
  newData.value = { nome: '', idade: 0 };
  readData();
};

onMounted(readData);
</script>

<style scoped>
.gradient-text {
  background: linear-gradient(to right, #34495e, #41b883);
  -webkit-background-clip: text;
  color: transparent;
  display: inline-block;
  font-family: "Fira Sans", sans-serif;
}

body {
  background-color: #1a1a1a;
  color: #ffffff;
  font-family: Arial, sans-serif;
}

.container {
  margin: 20px;
  padding: 20px;
}

form {
  display: flex;
  flex-direction: column;
  margin-bottom: 20px;
}

label {
  margin-bottom: 8px;
  font-weight: bold;
}

input {
  padding: 8px;
  margin-bottom: 16px;
  background-color: #333;
  color: #fff;
  border: 1px solid #555;
  border-radius: 4px;
}

button {
  padding: 8px;
  background-color: #41b883;
  /* background-color: transparent; */
  color: #fff;
  cursor: pointer;
  border: none;
  border-radius: 4px;
  margin-top: 8px;
}

button.cancel {
  background-color: #dc3545;
}

table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
}

th,
td {
  padding: 12px;
  border: 1px solid #555;
  text-align: left;
}

th {
  background-color: #41b883;
  color: #fff;
}

tr:nth-child(even) {
  background-color: #333;
}

button.delete {
  background-color: #dc3545;
}

td button {
  margin-right: 5px;
}

.action-column {
  text-align: center;
}

.action-column button {
  margin-right: 5px;
}
</style>

<!-- <script setup>
import { RouterLink, RouterView } from 'vue-router'
import HelloWorld from './components/HelloWorld.vue'
import CreateView from './views/CreateView.vue';
</script> -->

<!-- <template> -->
  <!-- <header> -->
  <!-- <img alt="Vue logo" class="logo" src="@/assets/logo.svg" width="125" height="125" /> -->

  <!-- <div class="wrapper"> -->
  <!-- <HelloWorld msg="You did it!" /> -->

  <!-- <nav>
        <RouterLink to="/">Home</RouterLink>
        <RouterLink to="/about">About</RouterLink>
        <RouterLink to="/form">Form</RouterLink>

      </nav>
    </div>
  </header>

  <RouterView /> -->
<!-- </template> -->

<!-- <style scoped>
header {
  line-height: 1.5;
  max-height: 100vh;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

nav {
  width: 100%;
  font-size: 12px;
  text-align: center;
  margin-top: 2rem;
}

nav a.router-link-exact-active {
  color: var(--color-text);
}

nav a.router-link-exact-active:hover {
  background-color: transparent;
}

nav a {
  display: inline-block;
  padding: 0 1rem;
  border-left: 1px solid var(--color-border);
}

nav a:first-of-type {
  border: 0;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }

  nav {
    text-align: left;
    margin-left: -1rem;
    font-size: 1rem;

    padding: 1rem 0;
    margin-top: 1rem;
  }
}
</style> -->

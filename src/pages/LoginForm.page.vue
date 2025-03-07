<template>
  <div class="container">
    <div class="tabs">
      <button :class="{ active: isLogin }" @click="isLogin = true">Connexion</button>
      <button :class="{ active: !isLogin }" @click="isLogin = false">Inscription</button>
    </div>

    <form v-if="isLogin" @submit.prevent="login">
      <label>Email</label>
      <input type="email" v-model="loginForm.email" required />

      <label>Mot de passe</label>
      <input type="password" v-model="loginForm.password" required />

      <button type="submit">Se connecter</button>
      <p>Pas de compte ? <span @click="isLogin = false">S'inscrire</span></p>
    </form>

    <form v-else @submit.prevent="register">
      <label>Email</label>
      <input type="email" v-model="registerForm.email" required />

      <label>Mot de passe</label>
      <input type="password" v-model="registerForm.password" required />

      <label>Confirmer le mot de passe</label>
      <input type="password" v-model="registerForm.confirmPassword" required />

      <button type="submit">S'inscrire</button>
      <p>Déjà un compte ? <span @click="isLogin = true">Se connecter</span></p>
    </form>
  </div>
</template>

<script setup>
import { ref } from "vue";

const isLogin = ref(true);
const loginForm = ref({ email: "", password: "" });
const registerForm = ref({ email: "", password: "", confirmPassword: "" });

const API_URL = "https://pokemon-api-seyrinian-production.up.railway.app";

async function login() {
  try {
    const response = await fetch(`${API_URL}/users/login`, {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(loginForm.value),
    });

    const data = await response.json();
    if (response.ok) {
      localStorage.setItem("token", data.token);
      localStorage.setItem("userId", data.user.id);
      window.location.href = "/deckbuilder";
    } else {
      alert(data.message || "Erreur lors de la connexion");
    }
  } catch (error) {
    console.error(error);
    alert("Problème de connexion au serveur.");
  }
}

async function register() {
  if (registerForm.value.password !== registerForm.value.confirmPassword) {
    alert("Les mots de passe ne correspondent pas !");
    return;
  }

  try {
    const response = await fetch(`${API_URL}/users`, {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({
        email: registerForm.value.email,
        password: registerForm.value.password,
      }),
    });

    const data = await response.json();
    if (response.ok) {
      isLogin.value = true;
    } else {
      alert(data.message || "Erreur lors de l'inscription");
    }
  } catch (error) {
    console.error(error);
    alert("Problème de connexion au serveur.");
  }
}
</script>

<style scoped>
.container {
  width: 300px;
  margin: auto;
  text-align: center;
}

.tabs {
  display: flex;
  justify-content: center;
  margin-bottom: 10px;
}

.tabs button {
  padding: 10px;
  flex: 1;
  border: none;
  cursor: pointer;
  background: lightgray;
}

.tabs .active {
  background: green;
  color: white;
}

form {
  display: flex;
  flex-direction: column;
}

input {
  margin: 5px 0;
  padding: 8px;
}

button {
  background: green;
  color: white;
  border: none;
  padding: 10px;
  cursor: pointer;
}

p span {
  color: blue;
  cursor: pointer;
}
</style>
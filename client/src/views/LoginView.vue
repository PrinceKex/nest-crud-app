<script setup lang="ts">
import { ref } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()
const username = ref('')
const password = ref('')
const error = ref('')

const login = async () => {
  try {
    const response = await fetch('http://localhost:3000/auth/login', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        username: username.value,
        password: password.value,
      }),
    })
    const data = await response.json()
    if (response.ok) {
      localStorage.setItem('access_token', data.access_token)
      router.push('/')
    } else {
      error.value = data.message || 'Login failed'
    }
  } catch (err) {
    error.value = 'An error occurred during login'
  }
}
</script>

<template>
  <div class="auth-container">
    <div class="auth-section">
      <h2>Login</h2>
      <form @submit.prevent="login">
        <input 
          v-model="username" 
          placeholder="Username" 
          required 
        />
        <input 
          v-model="password" 
          type="password" 
          placeholder="Password" 
          required 
        />
        <div class="form-actions">
          <button type="submit">Login</button>
        </div>
        <p v-if="error" class="error-message">{{ error }}</p>
      </form>
    </div>
  </div>
</template>

<style scoped>
.auth-container {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  padding: 20px;
  background-color: #f5f5f5;
}

.auth-section {
  background-color: white;
  padding: 30px;
  border-radius: 8px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  width: 100%;
  max-width: 400px;
}

form {
  display: flex;
  flex-direction: column;
  gap: 15px;
}

input {
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
}

.form-actions {
  display: flex;
  justify-content: center;
}

button {
  width: 100%;
  padding: 10px;
  background-color: #4CAF50;
  color: white;
  border: none;
  border-radius: 4px;
  font-size: 1rem;
  cursor: pointer;
  margin-top: 1rem;
}

.login-btn:hover {
  background-color: #1976d2;
}

.register-link {
  text-align: center;
  margin-top: 1rem;
  color: #666;
}

.register-link a {
  color: #2196f3;
  text-decoration: none;
}

.register-link a:hover {
  text-decoration: underline;
}
</style>

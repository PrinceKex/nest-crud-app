<script setup lang="ts">
import { ref, onMounted } from 'vue'
import { useRouter } from 'vue-router'

interface User {
  id: string;
  username: string;
  email: string;
}

const router = useRouter()
const users = ref<User[]>([])
const error = ref('')
const editingUser = ref<User | null>(null)
const newUsername = ref('')
const newPassword = ref('')
const newEmail = ref('')

// Fetch users with authentication
const fetchUsers = async () => {
  try {
    const token = localStorage.getItem('access_token')
    if (!token) {
      router.push('/login')
      return
    }

    const response = await fetch('http://localhost:3000/users', {
      method: 'GET',
      headers: {
        'Authorization': `Bearer ${token}`,
        'Content-Type': 'application/json'
      }
    })

    if (response.ok) {
      users.value = await response.json()
    } else {
      error.value = 'Failed to fetch users'
      router.push('/login')
    }
  } catch (err) {
    error.value = 'An error occurred while fetching users'
  }
}

// Delete a user
const deleteUser = async (userId: string) => {
  try {
    const token = localStorage.getItem('access_token')
    const response = await fetch(`http://localhost:3000/users/${userId}`, {
      method: 'DELETE',
      headers: {
        'Authorization': `Bearer ${token}`,
        'Content-Type': 'application/json'
      }
    })

    if (response.ok) {
      users.value = users.value.filter(user => user.id !== userId)
    } else {
      error.value = 'Failed to delete user'
    }
  } catch (err) {
    error.value = 'An error occurred while deleting user'
  }
}

// Start editing a user
const startEditing = (user: User) => {
  editingUser.value = { ...user }
  newUsername.value = user.username
  newEmail.value = user.email
}

// Update user
const updateUser = async () => {
  if (!editingUser.value) return

  try {
    const token = localStorage.getItem('access_token')
    const response = await fetch(`http://localhost:3000/users/${editingUser.value.id}`, {
      method: 'PATCH',
      headers: {
        'Authorization': `Bearer ${token}`,
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        username: newUsername.value,
        email: newEmail.value,
        ...(newPassword.value && { password: newPassword.value })
      })
    })

    if (response.ok) {
      const updatedUser = await response.json()
      const index = users.value.findIndex(u => u.id === updatedUser.id)
      if (index !== -1) {
        users.value[index] = updatedUser
      }
      editingUser.value = null
      newUsername.value = ''
      newEmail.value = ''
      newPassword.value = ''
    } else {
      error.value = 'Failed to update user'
    }
  } catch (err) {
    error.value = 'An error occurred while updating user'
  }
}

// Logout
const logout = () => {
  localStorage.removeItem('access_token')
  router.push('/login')
}

onMounted(fetchUsers)
</script>

<template>
  <div class="home-container">
    <div class="header">
      <h1>User Management</h1>
      <button @click="logout" class="logout-btn">Logout</button>
    </div>

    <div v-if="error" class="error-message">
      {{ error }}
    </div>

    <table v-if="users.length" class="user-table">
      <thead>
        <tr>
          <th>ID</th>
          <th>Username</th>
          <th>Email</th>
          <th>Actions</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="user in users" :key="user.id" v-bind:user="user">
          <td>{{ user.id }}</td>
          <td>
            <span v-if="editingUser?.id !== user.id">{{ user.username }}</span>
            <input 
              v-else 
              v-model="newUsername" 
              placeholder="New username"
            />
          </td>
          <td>
            <span v-if="editingUser?.id !== user.id">{{ user.email }}</span>
            <input 
              v-else 
              v-model="newEmail" 
              placeholder="New email"
              type="email"
            />
          </td>
          <td>
            <div v-if="editingUser?.id !== user.id" class="action-buttons">
              <button @click="startEditing(user)">Edit</button>
              <button @click="deleteUser(user.id)">Delete</button>
            </div>
            <div v-else class="edit-form">
              <input 
                v-model="newPassword" 
                type="password" 
                placeholder="New password (optional)"
              />
              <button @click="updateUser">Save</button>
              <button @click="editingUser = null">Cancel</button>
            </div>
          </td>
        </tr>
      </tbody>
    </table>
    <p v-else>No users found.</p>
  </div>
</template>

<style scoped>
.home-container {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
}

.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
}

.user-table {
  width: 100%;
  border-collapse: collapse;
}

.user-table th, .user-table td {
  border: 1px solid #ddd;
  padding: 8px;
  text-align: left;
}

.action-buttons {
  display: flex;
  gap: 10px;
}

.edit-form {
  display: flex;
  gap: 10px;
}

.error-message {
  color: red;
  margin-bottom: 15px;
}

.logout-btn {
  background-color: #f44336;
  color: white;
  border: none;
  padding: 8px 16px;
  cursor: pointer;
}
</style>

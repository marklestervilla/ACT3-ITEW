<template>
    <div class="login">
      <h1>Login</h1>
      <form @submit.prevent="login">
        <label for="username">Username:</label>
        <input type="text" id="username" v-model="username" required>
        <label for="password">Password:</label>
        <input type="password" id="password" v-model="password" required>
        <button type="submit">Login</button>
      </form>
      <p v-if="error" class="error">{{ error }}</p>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        username: '',
        password: '',
        error: ''
      };
    },
    methods: {
        login() {
        // Simulate asynchronous login process
        setTimeout(() => {
            if (this.username === 'admin' && this.password === 'password') {
            localStorage.setItem('token', '12345'); 
            this.$router.push({ name: 'home' }); 
            this.error = ''; 
            } else {
            this.error = 'Invalid username or password';
            }
        }, 500);
      }
    }
  };
  </script>
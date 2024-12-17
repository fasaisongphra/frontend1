<template>
    <div class="create-account-container">
      <h1>Create Account</h1>
      <form @submit.prevent="handleSubmit">
        <!-- <div class="form-group">
          <label for="username" style="font-size: 20px;">Username</label>
          <input type="text" id="username" v-model="formData.username" required />
        </div> -->
        <div class="form-group">
          <label for="email" style="font-size: 20px;">Username or Email</label>
          <input type="email" id="email" v-model="formData.email" required />
        </div>
        <div class="form-group">
          <label for="password" style="font-size: 20px;">Password</label>
          <input type="password" id="password" v-model="formData.password" required />
        </div>
        <button type="submit" class="submit-button">Create Account</button>
      </form>
    </div>
  </template>
  
  <script>
    import { ref } from 'vue'
    import axios from 'axios'
    import {useRouter} from  'vue-router'

    const router = useRouter()

  export default {
    data() {
      return {
        formData: {
          username: '',
          email: '',
          password: '',
        },
      };
    },
    methods: {
      handleSubmit() {
        console.log('Account created:', this.formData);
        alert('Account created successfully!');
        router.push('/login');
      },
    },
  };
  </script>
  
  <style scoped>
  .create-account-container {
    max-width: auto;
    /* margin: 10px; */
    /* padding: 20px; */
    background-color: #a9c7c3;
    border-radius: 8px;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
    text-align: center;
  }
  
  h1 {
    margin: 100px;
    color: #2b5f56;
    font-size: 24px;
    margin-bottom: 20px;
  }
  
  .form-group {
    /* font-size: 50px; */
    margin-bottom: 15px;
    text-align: left;
    margin: 50px;
  }
  
  label {
    display: block;
    font-size: 14px;
    color: #2b5f56;
    margin-bottom: 5px;
  }
  
  input {
    width: 100%;
    padding: 10px;
    font-size: 14px;
    border: 1px solid #4c8479;
    border-radius: 4px;
    background-color: #f5f5f5;
  }
  
  input:focus {
    outline: none;
    border-color: #2b5f56;
  }
  
  .submit-button {
    margin: 100px;
    /* width: 100%; */
    padding: 10px 15px;
    background-color: #4c8479;
    color: #fff;
    font-size: 16px;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    transition: background-color 0.3s;
  }
  
  .submit-button:hover {
    background-color: #2b5f56;
  }
  </style>
  
<template>
    <v-container>
      <v-form @submit.prevent="submitForm">
        <v-text-field v-model="name" label="Name" required></v-text-field>
        <v-text-field v-model="email" label="Email" required></v-text-field>
        
        <!-- Avatar Upload -->
        <v-file-input
          label="Upload Avatar"
          accept="image/*"
          prepend-icon="mdi-camera"
          @change="onFileChange"
        ></v-file-input>
  
        <v-btn color="primary" type="submit" @Click="submitForm" >Submit</v-btn>
      </v-form>
  
      <!-- Preview Avatar -->
      <v-img v-if="avatar" :src="avatar" max-width="200"></v-img>
    </v-container>
  </template>
  
  <script>
  import axios from 'axios';
  import { ref } from 'vue'
  export default {
    setup() {
      const name = ref("")
      const email = ref("")
      const avatarFile = ref(null)
      const avatar = ref(null)
  
      const onFileChange = (file) => {
        // Convert file to Base64 for preview
        const reader = new FileReader()
        reader.onload = (e) => {
          avatar.value = e.target.result
          console.log('avatar.value=>',avatar.value)
        }
        reader.readAsDataURL(file)
        avatarFile.value = file
        console.log('avatarFile.value=>',avatarFile.value)
      }
  
      const submitForm = async () => {
        console.log('submitForm')
        const formData = new FormData()
        formData.append("name", name.value)
        formData.append("email", email.value)
        formData.append("avatar", avatarFile.value)
  
        try {
          const response = await fetch("http://localhost:3000/api/upprofile",formData)
          console.log('API Response:', response.data)
         
        } catch (error) {
          console.error("Error:", error)
        }
      }
  
      return {
        name,
        email,
        avatarFile,
        avatar,
        onFileChange,
        submitForm
      }
    }
  }
  </script>
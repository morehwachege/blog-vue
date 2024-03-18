<template>
    <div class="min-h-screen">
      <h1 class="text-center text-3xl font-bold text-purple-700">{{ blog.title }}</h1>
      <p class="text-center py-5">Created by {{ blog.created_by }}</p>
      <p class="text-center px-20">{{ blog.body }}</p>
    </div>
  </template>
  
  <script>
  import axios from 'axios'
  
  export default {
    data() {
      return {
        blog: {} // Initialize an empty object to store the blog data
      }
    },
    mounted() {
      this.fetchBlog() 
    },
    methods: {
      async fetchBlog() {
        try {
          const response = await axios.get(`/art/blogs/${this.$route.params.id}`) // Fetch blog data using the ID from the route parameter
          this.blog = response.data
        } catch (error) {
          console.error('Error fetching blog:', error)
        }
      }
    }
  }
  </script>
  
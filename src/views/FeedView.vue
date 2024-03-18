<template>
  <div class="max-w-xl mx-auto">
    <div class="main-right">
      <div class="p-12 bg-white border border-gray-200 rounded-lg">
        <h1 class="mb-6 text-2xl">Create Blog</h1>

        <form class="space-y-6" @submit.prevent="submitForm">
          <div>
            <label>Title</label><br>
            <input type="text" v-model="form.title" placeholder="Enter title"
              class="w-full mt-2 py-4 px-6 border border-gray-200 rounded-lg">
          </div>

          <div>
            <label>Body</label><br>
            <textarea v-model="form.body" placeholder="Enter body"
              class="w-full mt-2 py-4 px-6 border border-gray-200 rounded-lg"></textarea>
          </div>

          <div>
            <label>Category</label><br>
            <select v-model="selectedCategories" multiple class="w-full mt-2 py-4 px-6 border border-gray-200 rounded-lg">
              <option v-for="category in categories" :key="category.id" :value="category.id">{{ category.name }}</option>
            </select>
          </div>

          <template v-if="errors.length > 0">
            <div class="bg-red-300 text-white rounded-lg p-6">
              <p v-for="error in errors" :key="error">{{ error }}</p>
            </div>
          </template>

          <div>
            <button type="submit" class="py-4 px-6 bg-purple-600 text-white rounded-lg">Create Blog</button>
          </div>
        </form>
      </div>

      <div v-for="blog in blogs" :key="blog.id" class="my-10 p-6 bg-white border border-gray-200 rounded-lg mb-4">
        <router-link :to="{ name: 'blogdetail', params: { id: blog.id } }">
          <div className="w-full h-full">
            <h2 class="text-xl font-bold mb-2 capitalize">{{ blog.title }}</h2>
            <p class="text-gray-700">{{ blog.body.slice(0, 40) }}{{ blog.body.length >40 ? '...': '' }}</p>
            <p class="mt-2 text-gray-600">Created by <span class="text-purple-700 text-md font-semibold">{{ blog.created_by }}</span></p>
            <div  class="my-4 flex-row w-full h-full">
              <span v-for="(cat, index) in blog.categories" :key="index" class="mx-1 bg-purple-700 px-4 rounded-full py-2 text-gray-300">{{cat.name}}</span>
            </div>
          </div>
        </router-link>
      </div>
    </div>
    <div>


    </div>
  </div>
</template>

<script>
import axios from 'axios';


export default {
  data() {
    return {
      form: {
        title: '',
        body: '',
        category: [],
        created_by: null
      },
      errors: [],
      categories: [],
      selectedCategories: [],
      blogs: []

    }
  },
  mounted() {
    this.fetchBlogs()
    this.fetchCategories()
    this.setCreatedBy()
  },
  methods: {
    async fetchCategories() {
      try {
        const response = await axios.get('/art/blogs/categories/')
        this.categories = response.data || []
      } catch (error) {
        this.categories = []
        console.error('Error fetching categories:', error)
      }
    },
    setCreatedBy() {
      // Get user ID from local storage and assign it to created_by field
      this.form.created_by = localStorage.getItem('user.id');
    },
    
    async fetchBlogs() {
      try {
        const response = await axios.get('/art/blogs/')
        this.blogs = response.data.results || []
        console.log(this.blogs, 'haya ndiyo mambo')
      } catch (error) {
        this.blogs = []
        console.error('Error fetching blogs:', error)
      }
    },
    async submitForm() {

      this.errors = []
      if (!this.form.title) {
        this.errors.push('Title is required')
      }

      if (!this.form.body) {
        this.errors.push('Body is required')
      }

      if (this.selectedCategories.length === 0) {
        this.errors.push('Category is required')
      } else {
        this.form.category = this.selectedCategories.map(Number); 
      }

      if (this.errors.length === 0) {
        try {
          const response = await axios.post('/art/blogs/create/', this.form)
          this.blogs.unshift(response.data)
          console.log('Blog created:', response.data)
        } catch (error) {
          console.error('Error creating blog:', error)
        }
      }
    }
  }
}
</script>
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
    </div>
  </div>
</template>

<script>
import axios from 'axios'

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
      selectedCategories: []
    }
  },
  mounted() {
    this.fetchCategories()
    this.setCreatedBy()
  },
  methods: {
    async fetchCategories() {
      try {
        const response = await axios.get('/art/blogs/categories/')
        this.categories = response.data
      } catch (error) {
        console.error('Error fetching categories:', error)
      }
    },
    setCreatedBy() {
      // Get user ID from local storage and assign it to created_by field
      this.form.created_by = localStorage.getItem('user.id');
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
          console.log(this.form.categories, 'hii ni ya wale wasee')
          console.log(this.form, 'hii ni ya wale wasee wa backend')

          const response = await axios.post('/art/blogs/create/', this.form)
          console.log('Blog created:', response.data)
        } catch (error) {
          console.error('Error creating blog:', error)
        }
      }
    }
  }
}
</script>

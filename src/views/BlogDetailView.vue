<template>
    <div class="min-h-screen flex items-center flex-col">
        <h1 class="text-center text-3xl font-bold text-purple-700">{{ blog.title }}</h1>
        <p class="text-center py-2 text-gray-600">Created by <span class="text-purple-700 text-md font-semibold">{{
            blog.created_by
        }}</span> on {{ formatDate(blog.created_at) }}</p>

        <div class="my-2 flex flex-row w-full h-full justify-center items-center">
            <span v-for="(cat, index) in blog.categories" :key="index"
                class="mx-1 bg-purple-700 px-4 rounded-full py-2 text-gray-300">{{ cat.name }}</span>
        </div>
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
        },
        formatDate(dateString) {
            const date = new Date(dateString);
            const options = {
                year: 'numeric',
                month: 'long',
                day: 'numeric',
                hour: 'numeric',
                minute: 'numeric',
                second: 'numeric',
                timeZoneName: 'short'
            };

            return date.toLocaleString('en-US', options);
        },
    }
}
</script>
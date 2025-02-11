<script setup>
import { usePostsStore } from "@/stores/post";
import { onMounted, ref } from "vue";
import { RouterLink } from "vue-router";
import { useAuthStore } from "@/stores/auth";

const authStore = useAuthStore();
const { getAllPosts, deletePost } = usePostsStore();
const posts = ref([]);

onMounted(async () => {
  posts.value = await getAllPosts();
});
</script>

<template>
  <main class="container mx-auto p-6 max-w-6xl">
    <div v-if="posts.length > 0" class="space-y-8">
    <h1 class="text-4xl font-bold text-center text-[#eae3ea] mb-8">Legutóbbi posztok</h1>
<div class="text-center mt-4">
  <RouterLink :to="{ name: 'create' }" class="auth-btn">
    Új poszt
  </RouterLink>
</div>


      <div
        v-for="post in posts"
        :key="post.id"
        class="border-l-4 border-[#6ABC5C] pl-4 p-3 rounded-lg shadow-lg bg-[#FDFDFD] hover:shadow-xl transition-shadow duration-300">
        <h2 class="text-3xl font-bold text-[#131213] mb-2">
          {{ post.title }}
        </h2>
        <p class="text-mx text-[#C7C8C7] mb-4 text-right">
          Posted by  
          <span class="text-green-800 font-bold">
            {{ post.user.name }}
          </span>
        </p>

        <p class="text-[#131213] mb-4">
          {{ post.content }}
        </p>


        <div class="flex items-center gap-2 mb-4">
          <button 
            class="text-red-500 hover:text-red-700 transition-colors duration-200" 
            @click="post.liked = !post.liked">
            <span v-if="post.liked">❤️</span>
            <span v-else>🤍</span>
          </button>
          <span>{{ post.liked ? post.likes + 1 : post.likes }} likes</span>
        </div>


        <div class="mb-6">
          <h3 class="text-lg font-bold text-[#131213] mb-2">Comments</h3>
          <div v-if="post.comments && post.comments.length > 0">
            <div
              v-for="comment in post.comments"
              :key="comment.id"
              class="border-l-4 border-[#D1D1D1] pl-4 p-3 mb-4 rounded-lg">
              <p class="text-[#131213]">
                <span class="font-bold">{{ comment.user.name }}:</span> {{ comment.content }}
              </p>
            </div>
          </div>
          <div v-else class="text-[#C7C8C7]">No comments yet.</div>

          <form @submit.prevent="addComment(post)">
            <textarea
              v-model="post.newComment"
              placeholder="Add a comment..."
              class="w-full p-2 border border-gray-300 rounded-lg mb-4"
              rows="3"></textarea>
            <button type="submit" class="text-blue-500 font-bold">Post Comment</button>
          </form>
        </div>

        <div v-if="authStore.user && authStore.user.id === post.user_id" class="flex items-center gap-6 mt-6">
          <form @submit.prevent="deletePost(post)">
            <button class="text-red-500 font-bold px-2 py-1 border border-red-300">
              Delete
            </button>
          </form>

          <RouterLink
            :to="{ name: 'update', params: { id: post.id } }"
            class="text-green-500 font-bold px-2 py-1 border border-green-300">
            Update
          </RouterLink>
        </div>
      </div>
    </div>

    <div v-else class="text-center">
      <h2 class="text-2xl text-[#C7C8C7]">There are no posts</h2>
    </div>
  </main>
</template>

<style scoped>
textarea {
  resize: none;
}

.auth-btn {
      background-color: #6ABC5C;
      color: #131213;
      padding: 8px 16px;
      border-radius: 6px;
      font-weight: bold;
      transition: background 0.3s, transform 0.2s;
      margin: 15px;
    }
    
    .auth-btn:hover {
      background-color: #5aa34d;
      transform: scale(1.05);
    }
</style>



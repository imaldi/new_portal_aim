

<template>
  <div class="greetings">
    <h1 class="green">{{ msg }}</h1>
    <h3>
      <div class="p-4 bg-green-500">Text Json Here {{ products }}</div>
      
      <!-- You’ve successfully created a project with -->
      <!-- <a href="https://vitejs.dev/" target="_blank" rel="noopener">Vite</a> +
      <a href="https://vuejs.org/" target="_blank" rel="noopener">Vue 3</a>. What's next? -->
    </h3>
    <div class="container my-4">
  <h1 id="heading" class="text-3xl font-semibold">Top Headlines</h1>

  <div v-if="isLoading" class="my-4">
    <div class="flex items-center justify-center">
      <div class="animate-spin rounded-full border-t-4 border-blue-500 h-12 w-12"></div>
    </div>
  </div>

  <div v-else-if="articles.length > 0">
    <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-4 my-4" id="news">
      <div v-for="article in articles" :key="article.id">
        <div class="bg-white rounded-lg overflow-hidden shadow-lg">
          <img class="w-full h-40 object-cover object-center" :src="article.urlToImage" alt="image not available" />
          <div class="p-4">
            <h5 class="text-xl font-semibold mb-2">{{ article.title }}</h5>
            <p class="text-gray-700">{{ article.description }}</p>
            <a target="_blank" :href="article.url" class="mt-2 inline-block px-4 py-2 bg-blue-500 text-white rounded">Read More</a>
          </div>
        </div>
      </div>
    </div>

    <nav class="container mx-auto">
      <ul class="flex justify-between items-center">
        <li>
          <a
            class="text-blue-500 cursor-pointer"
            @click="fetchPreviousPage"
            :class="{ 'opacity-50 cursor-not-allowed': isPreviousDisabled }"
          >Previous</a>
        </li>

        <li>
          <a
            class="text-blue-500 cursor-pointer"
            @click="fetchNextPage"
            :class="{ 'opacity-50 cursor-not-allowed': isNextDisabled }"
          >Next</a>
        </li>
      </ul>
    </nav>
  </div>

  <div v-else>
    <AlertComponent :msg="errorMsg" />
  </div>
</div>

  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import AlertComponent from "../components/AlertComponent.vue";

export default defineComponent({
  name: "HomeView",
  components: {
    AlertComponent,
  },
  data() {
    return {
      articles: [] as any[],
      currentPage: 1,
      totalPages: null as number | null,
      isLoading: true,
      errorMsg: null as string | null,
    };
  },
  watch: {
    $route: {
      immediate: true,
      handler() {
        this.fetchData();
      },
    },
  },
  mounted() {
    this.fetchData(this.currentPage);
  },
  methods: {
    async fetchData(page: number) {
      this.isLoading = true;
      try {
        const response = await fetch(
          `https://newsapi.org/v2/top-headlines?country=in&pageSize=10&page=${page}&apiKey=2ebd068ccfbb4331914b7f62638837ac`
        );
        const data = await response.json();
        if (data.status == "ok") {
          this.articles = data.articles;
          this.totalPages = Math.ceil(data.totalResults / 10);
          this.isLoading = false;
        } else {
          this.articles = [];
          this.isLoading = false;
          this.errorMsg = "No articles found";
        }
      } catch (e) {
        // alert(e);
        this.isLoading = false;
        this.articles = [];
        this.errorMsg =
          "An error occurred, please check your network connection";
      }
    },
    fetchPreviousPage() {
      if (this.currentPage > 1) {
        this.currentPage--;
        this.fetchData(this.currentPage);
        this.scrollToTop();
      }
    },
    fetchNextPage() {
      if (this.currentPage < (this.totalPages || 1)) {
        this.currentPage++;
        this.fetchData(this.currentPage);
        this.scrollToTop();
      }
    },
    scrollToTop() {
      window.scrollTo({
        top: 0,
        behavior: "smooth",
      });
    },
  },
  computed: {
    isPreviousDisabled() {
      return this.currentPage === 1;
    },
    isNextDisabled() {
      return this.currentPage === (this.totalPages || 1);
    },
  },
});
</script>

<!-- <style scoped>
h1 {
  font-weight: 500;
  font-size: 2.6rem;
  position: relative;
  top: -10px;
}

h3 {
  font-size: 1.2rem;
}

.greetings h1,
.greetings h3 {
  text-align: center;
}

@media (min-width: 1024px) {
  .greetings h1,
  .greetings h3 {
    text-align: left;
  }
}
</style> -->

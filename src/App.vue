<template>
  <div>
    <h1 style="text-align: center">Paginated Table</h1>
    <!-- Additional seach bar -->

    <div style="text-align: center">
      <input
        type="text"
        v-model="searchQuery"
        placeholder="Search by body..."
        style="margin-bottom: 1rem; width: 80%; padding: 0.5rem"
      />
    </div>
    <table border="1" style="width: 100%; text-align: left">
      <thead>
        <tr>
          <th>ID</th>
          <th>Title</th>
          <th>Body</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="post in paginatedData" :key="post.id">
          <td>{{ post.id }}</td>
          <td>{{ post.title }}</td>
          <td>{{ post.body }}</td>
        </tr>
      </tbody>
    </table>
    <div style="margin-top: 1rem; text-align: center">
      <!-- button pagination page -->
      <button :disabled="currentPage === 1" @click="currentPage--">
        Previous
      </button>
      <span>Page {{ currentPage }} of {{ totalPages }}</span>
      <button :disabled="currentPage === totalPages" @click="currentPage++">
        Next
      </button>
    </div>
  </div>
</template>

<script>
import { ref, computed, onMounted } from "vue";

export default {
  name: "PaginatedTable",
  setup() {
    // Declare a reactive variable,currentPage,and itemPerPage for storing posts
    const posts = ref([]);
    const currentPage = ref(1);
    const itemsPerPage = 10;
    const searchQuery = ref(""); // search query

    // Fetch data on component mount
    const fetchPosts = async () => {
      try {
        const response = await fetch(
          "https://jsonplaceholder.typicode.com/posts"
        );
        if (!response.ok) throw new Error("Failed to fetch posts");
        posts.value = await response.json(); // Store the data in the reactive variable
      } catch (error) {
        console.error("Error fetching posts:", error);
      }
    };

    //search Logic
    const filteredPosts = computed(() => {
      if (!searchQuery.value) return posts.value;
      return posts.value.filter((post) =>
        post.body.toLowerCase().includes(searchQuery.value.toLowerCase())
      );
    });

    // Pagination Logic
    const totalPages = computed(() =>
      Math.ceil(posts.value.length / itemsPerPage)
    );
    const paginatedData = computed(() => {
      const start = (currentPage.value - 1) * itemsPerPage;
      const end = start + itemsPerPage;
      return filteredPosts.value.slice(start, end);
    });

    // Use the onMounted lifecycle hook to call fetchPosts
    onMounted(() => {
      fetchPosts();
    });

    return {
      posts, // Expose the posts variable to the template
      searchQuery, //Search Query
      currentPage, //current page state
      totalPages, // Total pages state
      paginatedData, //Data for the current page
    };
  },
};
</script>

<style scoped>
/* table {
  border-collapse: collapse;
} */
th,
td {
  padding: 0.5rem;
}
h1 {
  color: #333;
}
li {
  margin-bottom: 1rem;
}
input {
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 0.8rem;
}
button {
  padding: 0.5rem 1rem;
  margin: 0 0.5rem;
}
button:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}
</style>

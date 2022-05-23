<template>
  <div>
    <h1 style="text-align: center; color: darkred">
      Сторінка з постами
    </h1>
    <my-input
      v-model="searchQuery"
      placeholder="Пошук..."
    />
    <div class="app-btns">
      <my-button @click="showDialog"
        >Створити пост</my-button
      >
      <my-select
        v-model="selectedSort"
        :options="sortOptions"
      />
    </div>
    <my-dialog v-model:show="dialogVisible">
      <post-form @create="createPost" />
    </my-dialog>
    <post-list
      :posts="sortedandSearchedPosts"
      для
      watch
      нужно
      заменить
      sortedPosts
      на
      post
      @remove="removePost"
      v-if="!isPostsLoading"
    />
    <div v-else>Йде завантаження...</div>
    <div ref="observer" class="observer"></div>
    <!-- <div class="page-wrapper">
      <div
        v-for="pageNumber in totalPages"
        :key="pageNumber"
        class="page"
        :class="{
          'current-page': page === pageNumber,
        }"
        @click="changePage(pageNumber)"
      >
        {{ pageNumber }}
      </div>
    </div> //!это обычное листание -->
  </div>
</template>

<script>
import PostForm from "@/components/PostForm.vue";
import PostList from "@/components/PostList.vue";
import MyButton from "@/components/UI/MyButton.vue";
import axios from "axios";
import MySelect from "@/components/UI/MySelect.vue";
import MyInput from "@/components/UI/MyInput.vue";
export default {
  components: {
    PostForm,
    PostList,
    MyButton,
    MySelect,
    MyInput,
  },
  data() {
    return {
      posts: [],
      dialogVisible: false,
      isPostsLoading: false,
      selectedSort: "",
      searchQuery: "",
      page: 1,
      limit: 10,
      totalPages: 0,
      sortOptions: [
        { value: "title", name: "По назві" },
        { value: "body", name: "По змісту" },
      ],
    };
  },
  methods: {
    createPost(post) {
      this.posts.push(post);
      this.dialogVisible = false;
    },
    removePost(post) {
      this.posts = this.posts.filter(
        (p) => p.id !== post.id
      );
    },
    showDialog() {
      this.dialogVisible = true;
    },
    // changePage(pageNumber) {
    //   this.page = pageNumber;
    // }, //!это если просто листать
    async fetchPosts() {
      this.isPostsLoading = true;
      try {
        const response = await axios.get(
          "https://jsonplaceholder.typicode.com/posts",
          {
            params: {
              _page: this.page,
              _limit: this.limit,
            },
          }
        );
        this.totalPages = Math.ceil(
          response.headers["x-total-count"] / this.limit
        );
        this.posts = response.data;
      } catch (e) {
        alert("Помилка");
      } finally {
        this.isPostsLoading = false;
      }
    },
    async loadMorePosts() {
      try {
        this.page += 1;
        const response = await axios.get(
          "https://jsonplaceholder.typicode.com/posts",
          {
            params: {
              _page: this.page,
              _limit: this.limit,
            },
          }
        );
        this.totalPages = Math.ceil(
          response.headers["x-total-count"] / this.limit
        );
        this.posts = [...this.posts, ...response.data];
      } catch (e) {
        alert("Помилка");
      }
    },
  },
  mounted() {
    this.fetchPosts();
    console.log(this.$refs.observer);
    const options = {
      rootMargin: "0px",
      threshold: 1.0,
    };
    const callback = (entries, observer) => {
      if (
        entries[0].isIntersecting &&
        this.page < this.totalPages
      ) {
        this.loadMorePosts();
      }
    };
    const observer = new IntersectionObserver(
      callback,
      options
    );
    observer.observe(this.$refs.observer);
  },
  computed: {
    sortedPosts() {
      return [...this.posts].sort((post1, post2) => {
        return post1[this.selectedSort]?.localeCompare(
          post2[this.selectedSort]
        );
      });
    },
    sortedandSearchedPosts() {
      return this.sortedPosts.filter((post) =>
        post.title
          .toLowerCase()
          .includes(this.searchQuery.toLowerCase())
      );
    },
  },

  watch: {
    // page() {
    //   this.fetchPosts();
    // },//!для обычного клацанья по стр
  },
  //   selectedSort(newValue) {
  //     this.posts.sort((post1, post2) => {
  //       return post1[this.selectedSort]?.localeCompare(
  //         post2[this.selectedSort]
  //       );
  //     });
  //   },
  // }, - //! - по сути тоже самое что и с computed
};
</script>

<style>
.app-btns {
  display: flex;
  justify-content: space-between;
  margin: 15px;
}
.page-wrapper {
  display: flex;
  margin-top: 15px;
}
.page {
  border: 1px solid darkred;
  padding: 10px;
  cursor: pointer;
}
.current-page {
  border: 2px solid darkcyan;
}
.observer {
  height: 30px;
}
</style>

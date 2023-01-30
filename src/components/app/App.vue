<template>
  <div class="app font-monospace">
    <div class="content">
      <AppInfo
        :AllMoviesCount="movies.length"
        :Favoruite="movies.filter((c) => c.favoruite).length"
      />

      <div class="search-panel">
        <SearchPanel :updateTermHandler="updateTermHandler" />
        <AppFilter
          :updateFilterHandler="updateFilterHandler"
          :filterName="filter"
        />
      </div>
      <Box v-if="!movies.length && !isLoading"
        ><p class="fs-3 text-center text-danger">Kinolar yo'q</p></Box
      >

      <Box v-else-if="isLoading" class="d-flex justify-content-center">
        <Loader />
      </Box>

      <Movielist
        v-else
        :movies="onFilterHandler(onSearchHandler(movies, term), filter)"
        @onToggle="onToggleHandler"
        @onRemove="onRemoveHandler"
      />
      <Box class="d-flex justify-content-center">
        <nav aria-label="pagination">
          <ul class="pagination pagination-sm">
            <li
              v-for="pageNumber in totalPages"
              :key="pageNumber"
              :class="{ active: pageNumber === page }"
              @click="changePageHandler(pageNumber)"
            >
              <span class="page-link">{{ pageNumber }}</span>
            </li>
          </ul>
        </nav>
      </Box>
      <Movieaddform @createMovie="createMovie" />
    </div>
  </div>
</template>

<script>
import AppInfo from "@/components/applicationInfo/AppInfo.vue";
import SearchPanel from "../SearchPanel/searchpanel.vue";
import AppFilter from "../AppFilter/AppFilter.vue";
import Movielist from "../movielist/movie-list.vue";
import Movieaddform from "../movieaddform/movieaddform.vue";
import axios from "axios";

export default {
  components: {
    AppInfo,
    SearchPanel,
    AppFilter,
    Movielist,
    Movieaddform,
  },

  data() {
    return {
      movies: [],
      term: "",
      filter: "all",
      isLoading: false,
      limit: 10,
      page: 1,
      totalPages: 0,
    };
  },
  methods: {
    createMovie(item) {
      this.movies.push(item);
      this.name = "";
      this.viewers = "";
    },

    onToggleHandler({ id, prop }) {
      console.log(prop);

      this.movies = this.movies.map((item) => {
        if (item.id === id) {
          return { ...item, [prop]: !item[prop] };
        }
        return item;
      });
    },

    onRemoveHandler(id) {
      this.movies = this.movies.filter((k) => k.id !== id);
    },

    onSearchHandler(arr, term) {
      if (term.length === 0) return arr;

      return arr.filter((c) => c.name.toLowerCase().indexOf(term) > -1);
    },

    updateTermHandler(term) {
      this.term = term;
    },

    onFilterHandler(arr, filter) {
      switch (filter) {
        case "popular":
          return arr.filter((c) => c.like);
        case "mostViewers":
          return arr.filter((c) => c.viewers > 500);
        default:
          return arr;
      }
    },

    updateFilterHandler(filter) {
      this.filter = filter;
    },

    async fetchMovie() {
      try {
        this.isLoading = true;

        const response = await axios.get(
          "https://jsonplaceholder.typicode.com/posts?_limit=10",
          {
            params: {
              _limit: this.limit,
              _page: this.page,
            },
          }
        );
        const newarr = response.data.map((item) => ({
          id: item.id,
          name: item.title,
          like: false,
          favoruite: false,
          viewers: item.id * 10,
        }));
        this.totalPages = Math.ceil(
          response.headers["x-total-count"] / this.limit
        );
        this.movies = newarr;
      } catch (error) {
        alert(error.message);
      } finally {
        this.isLoading = false;
      }
    },
    changePageHandler(page) {
      this.page = page;
      this.fetchMovie();
    },
  },

  watch: {
    page() {
      console.log("page");
      this.fetchMovie();
    },
  },
  mounted() {
    this.fetchMovie();
  },
};
</script>

<style>
.app {
  height: 100vh;
  color: #000;
}

.content {
  width: 1000px;
  min-height: 700px;
  background-color: #fff;
  margin: 0 auto;
  padding: 5rem 0;
}

.search-panel {
  margin-top: 2rem;
  padding: 1.5rem;
  background-color: #fcfaf5;
  border-radius: 4px;
  box-shadow: 15px 15px 15px rgba(0, 0, 0, 0.15);
}
</style>

<template>
  <section>
    <fieldset class="searchField">
      <label for="search">Search: </label>
      <input
        type="text"
        id="search"
        v-model="searchTerm"
        class="search"
        placeholder="Name, year or director"
      />
    </fieldset>
    <table v-if="!isLoading" border="">
      <thead>
        <tr>
          <th
            v-for="thead in ['title', 'year', 'director']"
            :key="thead"
            @click="sort(thead)"
            :class="currentSort === thead ? currentSortDir : ''"
          >
            {{ thead.toUpperCase() }}
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(row, index) in sortedRows" :key="row.title + index">
          <td>{{ row.title }}</td>
          <td>{{ row.year }}</td>
          <td>{{ row.director ?? "-" }}</td>
        </tr>
      </tbody>
    </table>
    <p v-else>
      {{ renderPlaceholder }}
    </p>
    <MoviePagination
      v-if="!isLoading"
      :totalPages="list.length / 10"
      :page="page"
      :startPage="startPage"
      @prev-click="prevPage"
      @next-click="nextPage"
      @page-click="setPage"
    ></MoviePagination>
  </section>
</template>

<script>
import { USER_API } from "../constants";
import MoviePagination from "./MoviePagination.vue";

export default {
  name: "MovieTable",
  components: {
    MoviePagination,
  },
  data() {
    return {
      searchTerm: "",
      list: [],
      page: 1,
      startPage: 0,
      isLoading: true,
      hasError: false,
      currentSort: "title",
      currentSortDir: "asc",
    };
  },
  computed: {
    sortedRows() {
      // const listCopy: this.list.map(movie => movie)
      const filter = this.list.filter((movie) => {
        // for (const key in movie) {
        //   if (
        //     movie[key]
        //       .toString()
        //       .toLowerCase()
        //       .includes(this.searchTerm.toLowerCase())
        //   ) {
        //     return true;
        //   }

        //   return false;
        // }
        const match =
          movie.title.includes(this.searchTerm.toLowerCase()) ||
          movie.director
            ?.toLowerCase()
            .includes(this.searchTerm.toLowerCase()) ||
          movie.year?.toString().includes(this.searchTerm.toLowerCase());
        return match;
      });

      let start = this.page * 10 - 10;
      let end = start + 10;

      const compare = (a, b) => {
        let modifier = 1;
        if (this.currentSortDir === "desc") modifier = -1;
        if (a[this.currentSort] < b[this.currentSort]) return -1 * modifier;
        if (a[this.currentSort] > b[this.currentSort]) return 1 * modifier;
        return 0;
      };

      return filter.sort(compare).slice(start, end);
    },
    renderPlaceholder() {
      if (this.hasError) {
        return `Error: Porfavor recarge la página`;
      } else if (this.isLoading) {
        return `Esta cargando...`;
      }

      return "";
    },
  },
  methods: {
    async fetchList() {
      try {
        const list = await fetch(USER_API);
        this.list = await list.json();
      } catch (e) {
        this.hasError = true;
      }

      this.isLoading = false;
    },
    setPage(page) {
      this.page = page;
    },
    prevPage() {
      this.page--;

      if (this.page === this.startPage) {
        this.startPage -= 10;
      }
    },
    nextPage() {
      this.page++;

      if (this.page === this.startPage + 11) {
        this.startPage += 10;
      }
    },
    sort(prop) {
      if (prop === this.currentSort) {
        this.currentSortDir = this.currentSortDir === "asc" ? "desc" : "asc";
      }
      this.currentSort = prop;
    },
  },
  created() {
    // Simular retardo
    setTimeout(() => this.fetchList(), 1000);
    console.log("created");
  },
};
</script>

<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
.searchField {
  border: none;
  display: flex;
  align-items: center;
  justify-content: flex-end;
}
input.search {
  padding: 5px;
  font-size: 1.2em;
  margin: 10px;
}
table {
  table-layout: fixed;
  width: 100%;
}
table th {
  cursor: pointer;
  background: rgba(173, 216, 230, 0.4);
}
table .asc::after {
  content: "\261D";
}
table .desc::after {
  content: "\1F447";
}
</style>

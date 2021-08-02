<template>
  <section>
    <table v-if="!isLoading" border="">
      <thead>
        <tr>
          <th
            @click="sort('title')"
            :class="currentSort === 'title' ? currentSortDir : ''"
          >
            Title
          </th>
          <th
            @click="sort('year')"
            :class="currentSort === 'year' ? currentSortDir : ''"
          >
            Year
          </th>
          <th
            @click="sort('director')"
            :class="currentSort === 'director' ? currentSortDir : ''"
          >
            Director
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(row, index) in sortedRows" :key="row.title + index">
          <td>{{ row.title }}</td>
          <td>{{ row.year }}</td>
          <td>{{ row.director ?? "No se localiza el director" }}</td>
        </tr>
      </tbody>
    </table>
    <p v-else>
      {{ renderPlaceholder }}
    </p>
    <pagination-table
      v-if="!isLoading"
      :totalPages="10"
      :page="page"
      @prev-click="page--"
      @next-click="page++"
      @page-click="setPage"
    ></pagination-table>
  </section>
</template>

<script>
import { USER_API } from "../constants";
import PaginationTable from "./PaginationTable.vue";

export default {
  name: "TableMovie",
  components: {
    PaginationTable,
  },
  data() {
    return {
      list: [],
      page: 1,
      isLoading: true,
      hasError: false,
      currentSort: "title",
      currentSortDir: "asc",
    };
  },
  computed: {
    sortedRows() {
      const listCopy = this.list.map((row) => row);
      let start = this.page * 10 - 10;
      let end = start + 10;

      const compare = (a, b) => {
        let modifier = 1;
        if (this.currentSortDir === "desc") modifier = -1;
        if (a[this.currentSort] < b[this.currentSort]) return -1 * modifier;
        if (a[this.currentSort] > b[this.currentSort]) return 1 * modifier;
        return 0;
      };

      return listCopy.sort(compare).slice(start, end);
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
    sort(prop) {
      if (prop === this.currentSort) {
        this.currentSortDir = this.currentSortDir === "asc" ? "desc" : "asc";
      }
      this.currentSort = prop;
    },
  },
  beforeCreate() {
    console.log("beforeCreate");
  },
  created() {
    setTimeout(() => this.fetchList(), 3000);
    console.log("created");
  },
  beforeMount() {
    console.log("beforeMount");
  },
  mounted() {
    console.log("mounted");
  },
  beforeUnmount() {
    console.log("beforeUnmount");
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
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

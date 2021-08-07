<template>
  <section class="pagination">
    <button
      @click="$emit('prevClick', $event)"
      :disabled="page <= 1"
      class="pagination-button pagination-button--move"
    >
      Prev
    </button>
    <button
      v-for="(p, index) in [...Array(totalPages).keys()].slice(
        startPage,
        startPage + 10
      )"
      :key="p + 1"
      @click="$emit('pageClick', p + 1)"
      class="pagination-button"
      :class="{ selected: page === index + startPage + 1 }"
    >
      {{ p + 1 }}
    </button>
    <button
      @click="$emit('nextClick', $event)"
      :disabled="page >= totalPages"
      class="pagination-button pagination-button--move"
    >
      Next
    </button>
  </section>
</template>

<script>
export default {
  name: "MoviePagination",
  props: {
    totalPages: {
      type: Number,
      default: 1,
    },
    page: {
      type: Number,
      default: 1,
    },
    startPage: {
      type: Number,
      default: 0,
    },
  },
  emits: {
    prevClick: null,
    nextClick: null,
    pageClick: null,
  },
};
</script>

<style>
.pagination {
  display: flex;
  align-items: center;
  justify-content: flex-end;
  margin: 10px 0;
}
.pagination-button {
  cursor: pointer;
  background: rgba(173, 216, 230, 0.2);
  border: 1px solid lightblue;
  border-radius: 2px;
  padding: 10px;
  min-width: 35px;
}
.pagination-button:hover {
  background: rgba(173, 216, 230, 0.4);
}
.pagination-button--move {
  margin: 0 5px;
}
.selected,
.selected:hover {
  background: lightblue;
}
</style>

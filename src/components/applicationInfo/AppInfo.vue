<template>
  <box>
    <p class="fs-3 text-uppercase">barcha kinolar soni: {{ AllMoviesCount }}</p>
    <p class="fs-4 text-uppercase">ko'rilgan kinolar soni {{ Favoruite }}</p>
  </box>
</template>

<style scoped></style>

<script>
export default {
  props: {
    AllMoviesCount: {
      type: Number,
      required: true,
    },

    Favoruite: {
      type: Number,
      required: true,
    },
  },
};
</script>

<template>
  <div class="movie-add-form">
    <h3>Yangi kino qo'shish</h3>
    <form class="add-form d-flex" @submit.prevent>
      <Input class="new-movie-label" placeholder="Qanday kino" v-model="name" />
      <Input
        class="new-movie-label"
        placeholder="Necha marotaba ko'rilgan"
        v-model="viewers"
      />

      <primaryButton class="btn-outline-dark" type="submit" @click="addMovie">
        Qo'shish
      </primaryButton>
    </form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      name: "",
      viewers: "",
    };
  },

  methods: {
    addMovie() {
      if (!this.name || !this.viewers) return;
      const newmovie = {
        name: this.name,
        viewers: this.viewers,
        favoruite: false,
        like: true,
        id: Date.now(),
      };

      this.$emit("createMovie", newmovie);
    },
  },
};
</script>

<style>
.movie-add-form {
  margin-top: 2rem;
  padding: 1.5rem;
  background-color: #fcfaf5;
  border-radius: 4px;
  box-shadow: 15px 15px 15px rgba(0, 0, 0, 0.15);
}
</style>

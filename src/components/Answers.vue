<template>
  <div class="alert alert-success text-center">
    <h1>To je tačan odgovor!!!</h1>
    <span>
      <img
        class="img-responsive"
        style="margin: 0 auto"
        :src="randomGif()"
        alt=""
    /></span>
    
    <hr />
    <button class="btn btn-primary" @click="onNextQuestion">
      Sledeće pitanje
    </button>
  </div>
</template>

<script>
export default {
  methods: {
    onNextQuestion() {
      this.$emit("confirmed");
    },
    randomGif() {
 
      let randomNmbr = Math.random().toFixed(1) * 10;
      return require(`../assets/congrats${randomNmbr}.gif`)
      
    },
  },
};
</script>

<style  scoped>
</style>
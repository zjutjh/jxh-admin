<template>
  <div class="content has-text-centered">
    <p>
      <img width="200" src="~assets/logo.png" :alt="description">
    </p>

    <h1 class="is-title is-bold">浙江工业大学精弘网络</h1>


    <p></p>
  </div>
</template>

<script>
export default {

  data () {
    return this.$store.state.pkg
  }

}
</script>

<style lang="scss" scoped>
.is-title {
  text-transform: capitalize;
}
</style>

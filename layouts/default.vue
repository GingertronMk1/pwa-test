<template>
  <main>
    <Nuxt />
  </main>
</template>
<script>
export default {
  name: "DefaultLayout",
  props: {},
  data() {
    return {

    }
  },
  mounted() {
  },
  methods: {
  }
}
</script>

<style lang="scss">
  main {
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    align-items: stretch;
  }
</style>

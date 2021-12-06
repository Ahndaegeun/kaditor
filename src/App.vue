<template>
  <editor @exportContent="getContent"/>
</template>

<script>
import editor from './components/editor.vue'

export default {
  name: 'App',
  components: {
    editor
  },
  methods: {
    getContent(e) {
      alert(e)
    }
  }
}
</script>

<style>

</style>

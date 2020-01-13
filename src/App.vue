<template>
  <div class="page-container">
    <ReviewEditor></ReviewEditor>
  </div>
</template>

<script>
/* eslint-disable */
import ReviewEditor  from './components/ReviewEditor'
export default {
  name: 'app',
  components: {
    ReviewEditor
  },
  // data function avails data to the template
  data() {
    return {
      menuVisible:false,
    }
  }
}
</script>

<style>
  .md-app {
    max-height: 400px;
    border: 1px solid rgba(#000, .12);
  }

</style>

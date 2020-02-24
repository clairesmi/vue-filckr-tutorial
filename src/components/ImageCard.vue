<template>
  <li class="image-card">
    <img :src="image.url_n" :alt="image.title"/>
  </li>
</template>
<script>
export default {
  name: 'image-card',
  props: {
    image: Object,
  },
};
</script>
<style scoped>

</style>

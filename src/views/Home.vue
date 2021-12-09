<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png" class="m-auto" />
    <HelloWorld class="mt-4" msg="Welcome to Your Vue.js + TypeScript App" />
    <ImageSlider :imageList="imageList" />
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import HelloWorld from '@/components/HelloWorld.vue';
import ImageSlider from '@/components/ImageSlider.vue';

export default defineComponent({
  name: 'Home',
  components: {
    HelloWorld,
    ImageSlider,
  },
  setup() {
    const imageList = [
      { id: 1, url: 'https://unsplash.it/490/710?image=0' },
      { id: 2, url: 'https://unsplash.it/490/710?image=1024' },
      { id: 3, url: 'https://unsplash.it/490/710?image=1060' },
    ];

    return { imageList };
  },
});
</script>

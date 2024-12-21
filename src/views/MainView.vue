<template>
  <div class="main-vue">
    <ExamplePlayer />
    <Recorder />
  </div>
</template>

<script setup lang="ts">
import ExamplePlayer from '@/components/ExamplePlayer.vue';
import Recorder from '@/components/Recorder.vue';

</script>

<style scoped lang="scss">
.main-vue {
  width: 100%;
  max-width: 800px;
  margin: 0 auto;
}
</style>

<template>
  <div class="example-player">
    <div class="upload-container">
      <input type="file" ref="fileInput" accept="audio/mp3" @change="onFileChanged" style="display: none;">
      <button class="file-button" @click="openFileSelector">Upload MP3</button>
      <div class="drop-zone" :style="{ background: dropZoneBgColor }" @dragover.prevent="dropZoneBgColor = '#e0e0e0'"  @drop.prevent="drop"
          @dragleave.prevent="dropZoneBgColor = '#f9f9f9'">
          Drag and drop MP3 file here
      </div>
    </div>
    <p>檔名：{{ fileName }}</p>
    <audio controls ref="audioElement" @loadedmetadata="loadedmetadata" @pause="onPause" @timeupdate="onTimeUpdate"></audio>
    <ExampleRepeatRangeSelector v-model="range" :total-second="duration"  />
  </div>
  
</template>

<script lang="ts" setup>
import { ref } from 'vue'
import ExampleRepeatRangeSelector from './ExampleRepeatRangeSelector.vue'

const range = ref({
  minValue: 0,
  maxValue: 10
})

const fileInput = ref()
const fileName = ref('')

const openFileSelector = () => {
  if (fileInput.value) {
    fileInput.value.click()
  }
}

const onFileChanged = (event: any) => {
  const file = event.target.files[0];
  if (file) {
    setSelectedFile(file);
  }
}

const dropZoneBgColor = ref('transparent')
const audioElement = ref()
const duration = ref(10)

const loadedmetadata = () => {
  range.value.maxValue = audioElement.value.duration
  range.value.minValue = 0
  duration.value = audioElement.value.duration
}

const onPause = () => {
  if (audioElement.value) {
    if (audioElement.value.currentTime < range.value.minValue) {
      audioElement.value.currentTime = range.value.minValue
    } else if (audioElement.value.currentTime > range.value.maxValue) {
      audioElement.value.currentTime = range.value.maxValue
    }
  }
}

const onTimeUpdate = () => {
  if (audioElement.value) {
    if (audioElement.value.currentTime < range.value.minValue) {
      audioElement.value.currentTime = range.value.minValue
    } else if (audioElement.value.currentTime > range.value.maxValue) {
      audioElement.value.pause()
      audioElement.value.currentTime = range.value.minValue
    }
  }
}

const setSelectedFile = (file: any) => {
  if (["audio/mp3", "audio/mpeg"].includes(file.type)) {
    fileName.value = file.name
    const audioUrl = URL.createObjectURL(file);
    audioElement.value.src = audioUrl
  } else {
    alert("Please select a valid MP3 file.");
  }
}

const drop = (event: any) => {
  console.log('drop', event)
  dropZoneBgColor.value = "#f9f9f9";
  const file = event.dataTransfer.files[0];
  if (file) {
    setSelectedFile(file);
  }
}
</script>

<style lang="scss" scoped>
.example-player {
  width: 100%;
  // color: red;
  // border: 1px solid red;

  .range {
    width: 500px;
  }

  .upload-container {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
  }

  .file-button {
    width: 48%;
    border: 1px solid #333;
    border-radius: 4px;
    margin: 20px 0;
    box-sizing: border-box;
    padding: 20px 10px;
    text-align: center;
  }

  .drop-zone {
    width: 48%;
    border: 1px solid #333;
    border-radius: 4px;
    margin: 20px 0;
    box-sizing: border-box;
    padding: 20px 10px;
    text-align: center;
  }

  audio {
    margin-bottom: 20px;
    width: 100%;
  }
}
</style>


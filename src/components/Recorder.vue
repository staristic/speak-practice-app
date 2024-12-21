<template>
  <div class="recorder">
    <h2>記錄區</h2>
    <button class="record-button" @click="record">
      <PlayIcon v-if="!isRecording" />
      <StopIcon v-if="isRecording" />
    </button>
    <ul>
      <li v-for="(r, index) in recordedList" :key="index">
        <span>{{ index + 1 }}</span>
        <audio controls :src="r"></audio>
        <button @click="removeRecord(index)"><TrashIcon /></button>
      </li>
    </ul>
  </div>
</template>

<script lang="ts" setup>
import { ref } from 'vue'
import PlayIcon from './svg/PlayIcon.vue';
import StopIcon from './svg/StopIcon.vue';
import TrashIcon from './svg/TrashIcon.vue';

const recordedList = ref([] as any[])
const isRecording = ref(false)
let mediaRecorder = null as null | MediaRecorder

const removeRecord = (index: number) => {
  if (index >= 0 && index < recordedList.value.length) {
    recordedList.value.splice(index, 1)
  }
}

const addRecording = (audioBlob: any) => {
  const audioUrl = URL.createObjectURL(audioBlob);
  recordedList.value.push(audioUrl)
}

const record = async () => {
  if (!isRecording.value) {
    if (!mediaRecorder) {
      let recordedChunks = [] as any[]
      const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
      mediaRecorder = new MediaRecorder(stream);
      mediaRecorder.ondataavailable = (event) => {
        if (event.data.size > 0) {
          recordedChunks.push(event.data);
        }
      };
      mediaRecorder.onstop = () => {
        const audioBlob = new Blob(recordedChunks, { type: "audio/webm" });
        recordedChunks = [];
        addRecording(audioBlob);
      };
    }
    mediaRecorder.start();
    isRecording.value = true
  } else {
    if (mediaRecorder) {
      mediaRecorder.stop();
      isRecording.value = false
    }
  }
}

</script>

<style lang="scss" scoped>
.recorder {
  width: 100%;
  margin-top: 20px;
  border: 3px dotted #ccc;
  padding: 10px;
  box-sizing: border-box;
  
  button {
    border-radius: 20px;
    width: 40px;
    height: 40px;
    padding: 3px 0 0 1px;
    border: 1px solid #ccc;

    svg {
      width: 18px;
      height: 18px;
    }
  }

  ul {
    padding: 0;
  }

  li {
    padding: 0;
    list-style: none;
    display: flex;
    flex-direction: row;
    align-items: center;
    margin-bottom: 20px;

    audio {
      margin: 0 10px;
    }
  }
}
</style>


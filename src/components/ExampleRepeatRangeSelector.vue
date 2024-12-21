<template>
  <div class="example-repeat-range-selector">
    <MultiRangeSlider class="range"
      :min="0"
      :max="totalSecond"
      :minValue="model.minValue"
      :maxValue="model.maxValue"
      :labels="hoursLabel"
      :min-caption="hoursMinCaption"
      :max-caption="hoursMaxCaption"
      :step="0.1"
      :ruler="false"
      :label="false"
      @input="updateHoursValues"
    />
    <span>播放範圍：{{ model.minValue }}s ~ {{ model.maxValue }}s</span>
  </div>
</template>

<script lang="ts" setup>
import { computed } from 'vue'
import MultiRangeSlider from "multi-range-slider-vue"

defineProps({
  totalSecond: {
    type: Number,
    default: 1
  }
})

const model = defineModel({
  default: {
    minValue: 0,
    maxValue: 0
  }
})
// const hBarMinValue = ref(0)
// const hBarMaxValue = ref(props.totalSecond)

const emits = defineEmits(['update:modelValue'])

const updateHoursValues = (e: any) => {
  emits('update:modelValue', {
    minValue: e.minValue,
    maxValue: e.maxValue
  })
}

const hoursLabel = computed(() => {
  let labels = [];
  for (let i = 0; i <= 12; i++) {
    labels.push(`${i.toString().length === 1 ? "0" : ""}${i}:00`);
  }
  return labels;
})


const hoursMinCaption = computed(() => {
  let h = Math.floor(model.value.minValue / 60);
  let m = model.value.minValue % 60;
  let hh = h.toString().length === 1 ? "0" : "";
  let mm = m.toString().length === 1 ? "0" : "";

  return `${hh}${h}:${mm}${m}`;
})

const hoursMaxCaption = computed(() => {
  let h = Math.floor(model.value.maxValue  / 60);
  let m = model.value.maxValue % 60;
  let hh = h.toString().length === 1 ? "0" : "";
  let mm = m.toString().length === 1 ? "0" : "";
  return `${hh}${h}:${mm}${m}`;
})
</script>

<style lang="scss" scoped>
.example-repeat-range-selector {
  min-width: 400px;

  .multi-range-slider {
    border: none;
    padding: 0;
  }

  .range {
    margin-bottom: 10px;
  }
}
</style>


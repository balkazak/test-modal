<template>
  <div class="grid grid-cols-2 gap-4">
    <div class="relative">
      <input
        :type="isDateRange ? 'date' : 'number'"
        v-model="rangeStart"
        class="input-field"
        :placeholder="startLabel"
      />
    </div>
    <div class="relative">
      <input
        :type="isDateRange ? 'date' : 'number'"
        v-model="rangeEnd"
        class="input-field"
        :placeholder="endLabel"
      />
    </div>
  </div>
</template>

<script setup lang="ts">
import { defineProps, defineEmits, ref, watch } from 'vue'

const props = defineProps({
  isDateRange: { type: Boolean, default: false },
  modelValue: { type: Object, default: () => ({ start: '', end: '' }) },
  startLabel: { type: String, default: '' },
  endLabel: { type: String, default: '' },
})

const emit = defineEmits(['update:modelValue'])

const rangeStart = ref(props.modelValue.start)
const rangeEnd = ref(props.modelValue.end)

watch([rangeStart, rangeEnd], ([newStart, newEnd]) => {
  emit('update:modelValue', { start: newStart, end: newEnd })
})

watch(
  () => props.modelValue,
  (newValue) => {
    rangeStart.value = newValue.start
    rangeEnd.value = newValue.end
  },
  { immediate: true }
)
</script>

<style scoped>
.input-field {
  width: 100%;
  padding: 10px 12px;
  border: 1px solid #d1d5db;
  border-radius: 8px;
  background-color: #f9fafb;
  font-size: 16px;
  color: #374151;
  outline: none;
  transition: border-color 0.3s;
}

.input-field:focus {
  border-color: #3b82f6;
  background-color: white;
}

.input-field::placeholder {
  color: #6b7280;
  font-size: 14px;
  font-weight: 500;
}
</style>

<template>
  <div class="relative w-full">
    <div
      class="border border-gray-300 rounded-md p-2 flex flex-wrap gap-2 items-center bg-white"
      @click="toggleDropdown"
    >
      <template v-if="multiple">
        <span
          v-for="(option, index) in selectedValue"
          :key="index"
          class="bg-blue-500 text-white px-2 py-1 rounded-md flex items-center gap-1"
        >
          {{ option }}
          <button
            class="text-white ml-1"
            @click.stop="removeOption(option)"
          >
            &times;
          </button>
        </span>
      </template>
      
      <span v-if="!multiple && selectedValue" class="text-gray-700">{{ selectedValue }}</span>
      <input
        v-if="!multiple && !selectedValue"
        type="text"
        :placeholder="placeholder"
        class="focus:outline-none text-gray-500"
        disabled
      />

      <span class="ml-auto text-gray-500">&#9662;</span>
    </div>

    <div
      v-if="isDropdownOpen"
      class="absolute top-full left-0 right-0 border border-gray-300 rounded-md bg-white mt-1 max-h-48 overflow-auto z-10"
    >
      <ul>
        <li
          v-for="option in options"
          :key="option"
          class="p-2 hover:bg-gray-100 cursor-pointer"
          @click="selectOption(option)"
        >
          {{ option }}
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup lang="ts">
import { defineProps, defineEmits, ref, watch } from 'vue'

const props = defineProps({
  options: { type: Array, required: true },
  multiple: { type: Boolean, default: false },
  placeholder: { type: String, default: 'Select options' },
  modelValue: { type: [String, Array], default: '' }
})

const emit = defineEmits(['update:modelValue'])

const selectedValue = ref(props.multiple ? [] : '')

const isDropdownOpen = ref(false)

const toggleDropdown = () => {
  isDropdownOpen.value = !isDropdownOpen.value
}

const selectOption = (option) => {
  if (props.multiple) {
    if (Array.isArray(selectedValue.value) && !selectedValue.value.includes(option)) {
      selectedValue.value.push(option)
    }
  } else {
    selectedValue.value = option
    isDropdownOpen.value = false
  }
  emit('update:modelValue', selectedValue.value)
}

const removeOption = (option) => {
  if (props.multiple && Array.isArray(selectedValue.value)) {
    selectedValue.value = selectedValue.value.filter((item) => item !== option)
    emit('update:modelValue', selectedValue.value)
  }
}

watch(
  () => props.modelValue,
  (newValue) => {
    selectedValue.value = newValue
  },
  { immediate: true }
)
</script>

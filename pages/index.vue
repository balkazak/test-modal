<template>
  <div class="min-h-screen flex items-center justify-center bg-gray-100">
    <button
      @click="isModalOpen = true"
      class="px-6 py-3 bg-blue-600 text-white font-semibold rounded-md hover:bg-blue-700"
    >
      Стать резидентом
    </button>

    <CustomModal
      v-if="isModalOpen"
      :visible="isModalOpen"
      @close="isModalOpen = false"
    />
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import CustomModal from '~/components/CustomModal.vue'

const isModalOpen = ref(false)
</script>

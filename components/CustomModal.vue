<template>
  <div v-if="visible" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
    <div class="bg-white p-8 rounded-lg shadow-lg max-w-lg w-full relative">
      <button @click="closeModal" class="absolute top-2 right-2 text-gray-500 hover:text-gray-700">✕</button>
      <h2 class="text-2xl font-semibold text-center mb-6">Заполните заявку, чтобы стать резидентом</h2>

      <form @submit.prevent="submitForm" class="space-y-6">
        <div>
          <label class="block text-sm font-medium text-gray-700 mb-1">Наименование организации / ИП</label>
          <Field name="organization" type="text" placeholder="ООО «Абсолют»" class="input-field" />
          <ErrorMessage name="organization" class="text-red-500 text-sm mt-1" />
        </div>

        <div>
          <label class="block text-sm font-medium text-gray-700 mb-1">Контактный телефон</label>
          <Field name="phone" type="text" placeholder="+7 (900) 123-45-67" v-mask="'+7 (###) ###-##-##'" class="input-field" />
          <ErrorMessage name="phone" class="text-red-500 text-sm mt-1" />
        </div>

        <div>
          <label class="block text-sm font-medium text-gray-700 mb-1">Тип помещения</label>
          <BaseSelect v-model="selectedOptions" :multiple="true" name="premisesType" :options="['Производственная площадь', 'Офисное помещение', 'Складское помещение']" />
        </div>

        <div>
          <label class="block text-sm font-medium text-gray-700 mb-1">Адрес</label>
          <Field name="address" type="text" placeholder="Адрес помещения" class="input-field" />
          <ErrorMessage name="address" class="text-red-500 text-sm mt-1" />
        </div>

        <div>
          <label class="block text-sm font-medium text-gray-700 mb-1">Площадь помещения (м²)</label>
          <BaseRange v-model="areaRange" :isDateRange="false" startLabel="от" endLabel="до" />
          <ErrorMessage name="area" class="text-red-500 text-sm mt-1" />
        </div>

        <div>
          <label class="block text-sm font-medium text-gray-700 mb-1">Дата начала аренды</label>
          <BaseRange v-model="rentalDateRange" :isDateRange="true" startLabel="с" endLabel="по" />
          <ErrorMessage name="rentalDate" class="text-red-500 text-sm mt-1" />
        </div>

        <button type="submit" class="w-full bg-blue-600 text-white font-semibold rounded-md py-3 hover:bg-blue-700">Отправить</button>
      </form>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, defineProps, defineEmits } from "vue";
import { useForm, Field, ErrorMessage } from "vee-validate";
import * as yup from "yup";
import BaseSelect from "~/components/BaseSelect.vue";
import BaseRange from "~/components/BaseRange.vue";

const props = defineProps<{ visible: boolean }>();
const emit = defineEmits(["close"]);

const selectedOptions = ref([]);
const areaRange = ref({ start: '', end: '' });
const rentalDateRange = ref({ start: '', end: '' });

const schema = yup.object({
  organization: yup.string().required("Это поле обязательно"),
  phone: yup.string().required("Это поле обязательно"),
  premisesType: yup.array().min(1, "Выберите хотя бы один тип помещения").required("Это поле обязательно"),
  address: yup.string().required("Это поле обязательно"),
  area: yup.object({
    start: yup.number().min(0, "Укажите минимальное значение").required("Укажите минимальное значение"),
    end: yup.number().min(0, "Укажите максимальное значение").required("Укажите максимальное значение")
  }).required(),
  rentalDate: yup.object({
    start: yup.date().required("Укажите дату начала"),
    end: yup.date().required("Укажите дату окончания")
  }).required(),
});

const { handleSubmit, values } = useForm({
  validationSchema: schema,
  initialValues: {
    organization: "",
    phone: "",
    premisesType: [],
    address: "",
    area: { start: '', end: '' },
    rentalDate: { start: '', end: '' },
  },
});

const closeModal = () => {
  emit("close");
};

const submitForm = handleSubmit(async (data) => {
  try {
    const response = await fetch("/api/submit-form", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(data),
    });
    if (!response.ok) throw new Error("Ошибка при отправке данных");
    closeModal();
  } catch (error) {
    console.error(error);
    alert("Произошла ошибка. Попробуйте еще раз.");
  }
});
</script>


<style scoped>
.input-field {
  width: 100%;
  padding: 10px 12px;
  border: 1px solid #d1d5db;
  border-radius: 8px;
  background-color: #f9fafb;
  font-size: 16px;
  font-weight: 500;
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

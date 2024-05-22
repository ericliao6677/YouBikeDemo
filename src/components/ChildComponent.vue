<script setup>
import { ref } from 'vue';

const props = defineProps({
  modelValue: String
});

const emit = defineEmits(['update:modelValue']);

const current = ref(props.modelValue);

const updateValue = (value) => {
  emit('update:modelValue', value);
};

const pages = ref([]);

for (let i = 1; i <= 10; i++) {
  pages.value.push(i);
}
</script>
<template>
  <nav aria-label="Page navigation example">
    <ul class="pagination">
      <li class="page-item" @click="updateValue(current--)">
        <a class="page-link" href="#" aria-label="Previous">
          <span aria-hidden="true">&laquo;</span>
        </a>
      </li>

      <li class="page-item" v-for="item in pages" :key="item" @click="updateValue(item)">
        <a class="page-link" href="#">{{ item }}</a>
      </li>

      <li class="page-item" @click="updateValue(current++)">
        <a class="page-link" href="#" aria-label="Next">
          <span aria-hidden="true">&raquo;</span>
        </a>
      </li>
    </ul>
  </nav>
</template>

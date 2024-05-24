<script setup>
import { ref } from 'vue';

//const emit = defineEmits(['inputEventHandle']);
//const emit = defineEmits(['update:modelValue', 'test']);
const modelValue = defineModel('modelValue');
//const inputValue = ref('');
//emit('test', inputValue.value);
//emit('update:modelValue', inputValue.value);
</script>
<template>
  <div class="input-group mb-3 w-25">
    <input type="text" class="form-control" placeholder="輸入站點地址" v-model="modelValue" />
    <button class="btn btn-primary" type="button">搜尋</button>
  </div>
</template>
<style></style>

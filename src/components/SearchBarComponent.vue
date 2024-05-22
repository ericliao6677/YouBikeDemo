<script setup>
import { ref } from 'vue';

const emit = defineEmits(['inputEventHandle']);
const inputRef = ref(null);
const inputValue = ref('');
const inputEventHandle = () => {
  inputValue.value = inputRef.value.value;
  emit('inputEventHandle', inputValue.value);
};
</script>
<template>
  <div class="input-group mb-3 w-25">
    <input
      type="text"
      class="form-control"
      placeholder="輸入站點地址"
      ref="inputRef"
      @input="inputEventHandle"
    />
    <button class="btn btn-primary" type="button">搜尋</button>
  </div>
</template>
<style></style>

<script setup>
import { ref } from 'vue';
import ChildComponent from '../components/ChildComponent.vue';

const parentValue = ref('');
</script>
<template>
  <div>
    <ChildComponent v-model="parentValue"></ChildComponent>
    {{ parentValue }}
  </div>
</template>

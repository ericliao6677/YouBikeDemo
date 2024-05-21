<script setup>
import { ref, computed } from 'vue';

//宣告props，值從父元件傳過來
const props = defineProps({
  totalPages: {
    type: Number,
    required: true,
    default: 100
  },
  pageSize: {
    type: Number,
    required: true,
    default: 10
  },
  modelValue: {
    type: Number,
    required: true,
    default: 1
  }
});

//宣告子元件事件
const emit = defineEmits(['updatePage']);

const childCurrentPage = ref(props.modelValue);

const visiblePages = computed(() => {
  const pages = [];
  const half = Math.floor(props.pageSize / 2);
  //假設目前頁數為第7頁，那起始頁會是2，結束頁會是11
  let startPage = Math.max(childCurrentPage.value - half, 1);
  let endPage = startPage + props.pageSize - 1;

  if (endPage > props.totalPages) {
    endPage = props.totalPages;
    startPage = Math.max(endPage - props.pageSize + 1, 1);
  }

  for (let i = startPage; i <= endPage; i++) {
    pages.push(i);
  }

  return pages;
});

const changePage = (page) => {
  if (page > 0 && page <= props.totalPages) {
    childCurrentPage.value = page;
    emit('updatePage', childCurrentPage.value);
  }
};
</script>
<template>
  <div>
    <nav aria-label="Page navigation example">
      <ul class="pagination">
        <!--上一頁按鈕-->
        <li
          class="page-item"
          :class="{ disabled: childCurrentPage === 1 }"
          @click="changePage(childCurrentPage - 1)"
        >
          <a class="page-link" href="#" aria-label="Previous">
            <span aria-hidden="true">&laquo;</span>
          </a>
        </li>

        <!--依據頁數跑迴圈，並將目前頁數回傳給父元件-->
        <li class="page-item" v-for="item in visiblePages" :key="item" @click="changePage(item)">
          <a class="page-link" href="#">{{ item }}</a>
        </li>

        <!--下一頁按鈕-->
        <li
          class="page-item"
          :class="{ disabled: childCurrentPage === props.totalPages }"
          @click="changePage(childCurrentPage + 1)"
        >
          <a class="page-link" href="#" aria-label="Next">
            <span aria-hidden="true">&raquo;</span>
          </a>
        </li>
      </ul>
    </nav>
  </div>
</template>
<style></style>

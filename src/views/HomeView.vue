<script setup>
import { computed, onMounted, ref } from 'vue';
import PaginationItem from '../components/PaginationItem.vue';
import SearchBarComponent from '../components/SearchBarComponent.vue';

const allBikeList = ref([]);

const searchStr = ref(''); //關鍵字搜尋文字

const currentSort = ref(''); //目前排序的欄位
const sortType = ref(''); //排序類型

const pageSize = ref(10); //一頁10筆資料
const currentPage = ref(1); //預設為第1頁

const getYouBikeData = async () => {
  const res = await fetch(
    'https://tcgbusfs.blob.core.windows.net/dotapp/youbike/v2/youbike_immediate.json'
  );
  const resData = await res.json();
  allBikeList.value = resData;
};

//1. 關鍵字搜尋
//利用computed及時監聽searchStr.value並改變當前資料
const allBikeListFilterBySearchStr = computed(() => {
  return allBikeList.value.filter((item) => item.ar.includes(searchStr.value));
});

const hightSearchStr = (ar) => {
  if (searchStr.value) {
    return ar.replace(searchStr.value, `<span style=color:red>${searchStr.value}</span>`);
  } else {
    return ar;
  }
};

//2.排序
const sortAllBikeList = (sortCol) => {
  const originType = currentSort.value; //原本的排序欄位
  currentSort.value = sortCol; //之後欲排序的欄位

  //如果是原本的欄位就切換排序方式，否則預設為降冪排序
  if (originType === currentSort.value) {
    sortType.value = sortType.value === 'asc' ? 'desc' : 'asc';
  } else {
    sortType.value = 'desc';
  }
};

//利用computed可以及時監聽sortType.value回傳排序後的資料
const allBikeListFilterBySort = computed(() => {
  let result = [...allBikeListFilterBySearchStr.value];
  //如果currentSort.value沒有值就回傳原資料
  if (!currentSort.value) return result;
  return sortType.value === 'asc'
    ? result.sort((pre, next) => pre[currentSort.value] - next[currentSort.value])
    : result.sort((pre, next) => next[currentSort.value] - pre[currentSort.value]);
});

//3. 分頁
//排序後的總筆數
//用allBikeListFilterBySort，如果currentSort.value === '' => 回傳原資料，所以總比數不會錯
const allBikeListFilterBySortTotal = computed(() => allBikeListFilterBySort.value.length);

const allBikeListFilterBySortSliced = computed(() => {
  const skipCount = (currentPage.value - 1) * pageSize.value;
  const data = allBikeListFilterBySort.value.slice(skipCount, skipCount + pageSize.value);
  return data;
});

onMounted(async () => {
  await getYouBikeData();
});
</script>

<template>
  <div class="">
    <div class="d-flex justify-content-between">
      <div class="col-5">
        <h3>
          <strong>YouBike範本 </strong>
        </h3>
        <span>目前頁數: {{ currentPage }}</span>
      </div>
      <SearchBarComponent v-model="searchStr"></SearchBarComponent>{{ searchStr }}
    </div>
    <hr />
    <table class="table table-striped">
      <thead>
        <tr>
          <th scope="col">站點編號</th>
          <th scope="col">站點名稱</th>
          <th scope="col">站點所在區域</th>
          <th scope="col">站點地址</th>
          <th scope="col" @click="sortAllBikeList('total')">
            總車位數量
            <div class="d-flex flex-column">
              <i
                :class="[
                  currentSort === 'total' && sortType === 'asc' ? 'bi-caret-up-fill' : 'bi-caret-up'
                ]"
                class="bi icon-sm cursor-pointer"
              ></i>
              <i
                :class="[
                  currentSort === 'total' && sortType === 'desc'
                    ? 'bi-caret-down-fill'
                    : 'bi-caret-down'
                ]"
                class="bi icon-sm cursor-pointer"
              ></i>
            </div>
          </th>
          <th scope="col" @click="sortAllBikeList('available_rent_bikes')">
            可租借的腳踏車數量
            <div class="d-flex flex-column">
              <i
                :class="[
                  currentSort === 'available_rent_bikes' && sortType === 'asc'
                    ? 'bi-caret-up-fill'
                    : 'bi-caret-up'
                ]"
                class="bi icon-sm cursor-pointer"
              ></i>
              <i
                :class="[
                  currentSort === 'available_rent_bikes' && sortType === 'desc'
                    ? 'bi-caret-down-fill'
                    : 'bi-caret-down'
                ]"
                class="bi icon-sm cursor-pointer"
              ></i>
            </div>
          </th>
          <th scope="col">站點緯度</th>
          <th scope="col">站點經度</th>
          <th scope="col">可歸還的腳踏車數量</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="item in allBikeListFilterBySortSliced" :key="item.sno">
          <td>{{ item.sno }}</td>
          <td>{{ item.sna }}</td>
          <td>{{ item.sarea }}</td>
          <td v-html="hightSearchStr(item.ar)"></td>
          <td>{{ item.total }}</td>
          <td>{{ item.available_rent_bikes }}</td>
          <td>{{ item.latitude }}</td>
          <td>{{ item.longitude }}</td>
          <td>{{ item.available_return_bikes }}</td>
        </tr>
      </tbody>
    </table>
    <PaginationItem
      :totalData="allBikeListFilterBySortTotal"
      :pageSize="pageSize"
      v-model="currentPage"
    >
    </PaginationItem>
  </div>
</template>

<style scoped>
.custom-table {
  width: 100%;
}
.cursor-pointer {
  cursor: pointer;
}
.icon-sm {
  font-size: 12px; /* 調整到所需的大小 */
}
</style>

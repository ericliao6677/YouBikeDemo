<script setup>
import { ref } from 'vue';
import PaginationItem from '../components/PaginationItem.vue';

const allYouBikeDataList = ref([]);
const filterByPageSizeYouBikeDataList = ref([]);

const pageSize = ref(10); //一頁10筆資料
const currentPage = ref(1); //預設為第1頁
const totalDataCount = ref(0); //由api取得的資料的筆數
const totalPages = ref(0); //會由總筆數資料計算出需要的頁數

const getYouBikeData = async () => {
  const res = await fetch(
    'https://tcgbusfs.blob.core.windows.net/dotapp/youbike/v2/youbike_immediate.json'
  );
  const resData = await res.json();
  allYouBikeDataList.value = resData;
  totalDataCount.value = allYouBikeDataList.value.length;

  //計算出總共需要的頁數
  totalPages.value = Math.round(totalDataCount.value / pageSize.value) + 1;
  filterByPageSizeYouBikeDataList.value = allYouBikeDataList.value.slice(0, pageSize.value);
};

//currentPagefromChild的值是由子元件emit傳出
const updateCurrentPageData = (currentPagefromChild) => {
  const skipCount = (currentPagefromChild - 1) * pageSize.value;
  filterByPageSizeYouBikeDataList.value = allYouBikeDataList.value.slice(
    skipCount,
    skipCount + pageSize.value
  );
  currentPage.value = currentPagefromChild;
};
getYouBikeData();
</script>

<template>
  <div class="">
    <h3><strong>YouBike範本</strong></h3>
    <hr />
    <table class="table table-striped">
      <thead>
        <tr>
          <th scope="col">站點編號</th>
          <th scope="col">站點名稱</th>
          <th scope="col">站點所在區域</th>
          <th scope="col">站點地址</th>
          <th scope="col">總車位數量</th>
          <th scope="col">可租借的腳踏車數量</th>
          <th scope="col">站點緯度</th>
          <th scope="col">站點經度</th>
          <th scope="col">可歸還的腳踏車數量</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="item in filterByPageSizeYouBikeDataList" :key="item.sno">
          <td>{{ item.sno }}</td>
          <td>{{ item.sna }}</td>
          <td>{{ item.sarea }}</td>
          <td>{{ item.ar }}</td>
          <td>{{ item.total }}</td>
          <td>{{ item.available_rent_bikes }}</td>
          <td>{{ item.latitude }}</td>
          <td>{{ item.longitude }}</td>
          <td>{{ item.available_return_bikes }}</td>
        </tr>
      </tbody>
    </table>
    <PaginationItem
      :totalPages="totalPages"
      :pageSize="pageSize"
      v-model="currentPage"
      @updatePage="updateCurrentPageData"
    >
    </PaginationItem>
  </div>
</template>

<style scoped></style>

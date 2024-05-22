<script setup>
import { ref } from 'vue';
import PaginationItem from '../components/PaginationItem.vue';
import SearchBarComponent from '../components/SearchBarComponent.vue';

const allYouBikeDataList = ref([]);
const filterByPageSizeYouBikeDataList = ref([]);

const inputValueInParent = ref('');
const pageSize = ref(10); //一頁10筆資料
const currentPage = ref(1); //預設為第1頁
const totalDataCount = ref(0); //由api取得的資料的筆數
const totalPages = ref(0); //會由總筆數資料計算出需要的頁數

const totalSortUpIcon = ref('bi-caret-up');
const totalSortDownIcon = ref('bi-caret-down');
const availableSortUpIcon = ref('bi-caret-up');
const availableSortDownIcon = ref('bi-caret-down');

const getYouBikeData = async () => {
  const res = await fetch(
    'https://tcgbusfs.blob.core.windows.net/dotapp/youbike/v2/youbike_immediate.json'
  );
  const resData = await res.json();
  allYouBikeDataList.value = resData;
  totalDataCount.value = allYouBikeDataList.value.length;

  //計算出總共需要的頁數
  totalPages.value = Math.round(totalDataCount.value / pageSize.value) + 1;
  //filterByPageSizeYouBikeDataList.value = allYouBikeDataList.value.slice(0, pageSize.value);
  updateCurrentPageData(currentPage.value);
};
getYouBikeData();

//currentPagefromChild的值是由子元件emit傳出
const updateCurrentPageData = (currentPagefromChild) => {
  //可以在這邊覆值如下，或是使用v-model綁定
  //currentPage.value = currentPagefromChild;

  //計算需要略過幾筆資料
  const skipCount = (currentPagefromChild - 1) * pageSize.value;
  filterByPageSizeYouBikeDataList.value = allYouBikeDataList.value.slice(
    skipCount,
    skipCount + pageSize.value
  );
  //currentPage.value = currentPagefromChild;
};

const sortBy = (col, sortType) => {
  totalSortUpIcon.value = 'bi-caret-up';
  totalSortDownIcon.value = 'bi-caret-down';
  availableSortUpIcon.value = 'bi-caret-up';
  availableSortDownIcon.value = 'bi-caret-down';

  if (col === '總車位數量') {
    if (sortType === 'asc') {
      allYouBikeDataList.value.sort((pre, next) => {
        return pre.total - next.total;
      });
      //更改sort樣式
      totalSortUpIcon.value = 'bi-caret-up-fill';
      //根據排序過後的資料再做一次分頁
      updateCurrentPageData(currentPage.value);
    } else {
      allYouBikeDataList.value.sort((pre, next) => {
        return next.total - pre.total;
      });
      totalSortDownIcon.value = 'bi-caret-down-fill';
      updateCurrentPageData(currentPage.value);
    }
  }
  if (col === '可租借的腳踏車數量') {
    if (sortType === 'asc') {
      allYouBikeDataList.value.sort((pre, next) => {
        return pre.available_rent_bikes - next.available_rent_bikes;
      });
      //更改sort樣式
      availableSortUpIcon.value = 'bi-caret-up-fill';
      //根據排序過後的資料再做一次分頁
      updateCurrentPageData(currentPage.value);
    } else {
      allYouBikeDataList.value.sort((pre, next) => {
        return next.available_rent_bikes - pre.available_rent_bikes;
      });
      availableSortDownIcon.value = 'bi-caret-down-fill';
      updateCurrentPageData(currentPage.value);
    }
  }
};

const test = ref('');
const filterKeyWord = (keyWord) => {
  test.value = keyWord;
  allYouBikeDataList.value = allYouBikeDataList.value.filter((value) => value.ar.includes(keyWord));
  //updateCurrentPageData(currentPage.value);
};
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
      <SearchBarComponent
        v-model="inputValueInParent"
        @inputEventHandle="filterKeyWord"
      ></SearchBarComponent>
      {{ test }}
    </div>
    <hr />
    <table class="table table-striped">
      <thead>
        <tr>
          <th scope="col">站點編號</th>
          <th scope="col">站點名稱</th>
          <th scope="col">站點所在區域</th>
          <th scope="col">站點地址</th>
          <th scope="col">
            總車位數量
            <div class="d-flex flex-column">
              <i
                :class="totalSortUpIcon"
                class="bi icon-sm cursor-pointer"
                @click="sortBy('總車位數量', 'asc')"
              ></i>
              <i
                :class="totalSortDownIcon"
                class="bi icon-sm cursor-pointer"
                @click="sortBy('總車位數量', 'desc')"
              ></i>
            </div>
          </th>
          <th scope="col">
            可租借的腳踏車數量
            <div class="d-flex flex-column">
              <i
                :class="availableSortUpIcon"
                class="bi icon-sm cursor-pointer"
                @click="sortBy('可租借的腳踏車數量', 'asc')"
              ></i>
              <i
                :class="availableSortDownIcon"
                class="bi icon-sm cursor-pointer"
                @click="sortBy('可租借的腳踏車數量', 'desc')"
              ></i>
            </div>
          </th>
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

<script setup lang="ts">
import axios, { AxiosRequestConfig } from 'axios';
import { onMounted, ref } from 'vue';
import { ProductList } from './types/productList.ts';

const headers = {
  "User-Agent": "Mozilla/5.0 (Linux; Android 12; M2007J3SC Build/SKQ1.211006.001; wv) AppleWebKit/537.36 (KHTML, like Gecko) Version/4.0 Chrome/96.0.4664.104 Mobile Safari/537.36 MMWEBID/8424 MicroMessenger/8.0.42.2460(0x28002A35) WeChat/arm64 Weixin NetType/WIFI Language/zh_CN ABI/arm64",
  "Host": "wx.jwnzn.com",
  "Cookie": "JSESSIONID=622A9C442D7502056004451020816535",
  "Accept": "/",
  "Connection": "keep-alive"
};

const params = [
  {
    "name": "DogU-1号站西食堂南侧",
    "gpsId": "51266",
    "sn": "10168679375"
  },
  {
    "name": "DogU-6号站西食堂南侧",
    "gpsId": "51271",
    "sn": "10168674219"
  },
  {
    "name": "DogU-10号站西食堂南侧",
    "gpsId": "57064",
    "sn": "10753215260"
  }
]

let requestOptions: AxiosRequestConfig = {
      method: 'GET',
      headers,
      url: `api/njjwn/eleProductList.action?queryValue=gpsId%3A${params[0].gpsId}%3BdevType%3A2%3Bsn%3A${params[0].sn}%3B`
};
const data = ref<[{ listindex: number, value: ProductList[] }]>([{ listindex: 0, value: [] }]);
const dataLoaded = ref(false);

onMounted(() => {
  for (let i = 0; i < params.length; ++i) {
    requestOptions = {
      method: 'GET',
      headers,
      url: `api/njjwn/eleProductList.action?queryValue=gpsId%3A${params[i].gpsId}%3BdevType%3A2%3Bsn%3A${params[i].sn}%3B`
    }
    axios(requestOptions)
      .then(response => {
        let cur = response.data.list;
        data.value.push({ 'listindex': i, value: cur }); console.log(response.data.list); console.log(i); if (i == params.length - 1) {
          dataLoaded.value = true
        }
      })
      .catch(error => console.log('error', error));
  }
});

</script>

<template>
  <div>
    <h2>Welcome to DogU!</h2>
    <h3>笃学明德 经世致用</h3>
    <div v-if="dataLoaded">
      <div v-for="(list, index) in data" :key="index">
        <div v-if="index > 0">
          <div>{{ params[list.listindex].name }}</div>
          <div class="container">
            <div v-for="item in list.value" :key="item.id">
              <div v-if="item.status == '工作中'">
                <button class="btn btn-secondary">{{ item.name }}</button>
              </div>
              <div v-else-if="item.status == '空闲'">
                <button class="btn btn-success">{{ item.name }}</button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div v-else>
      Loading...
    </div>
  </div>
</template>

<style scoped types="scss">
h2{
  margin: 10px;
}
.container {
  display: flex;
  flex-direction: row;
  align-items: center;
  margin: 10px;
  .btn {
    align-content: center;
  }
}
</style>

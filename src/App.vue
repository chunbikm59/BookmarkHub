

<template>

  <label for="inputName">URL: </label>
  <input id="inputUrl" type="text" v-model="inputUrl">
  <button class="btn" @click="add">新增</button>
  <button @click="deleteAll">刪除全部</button>
  <br>
  <br>
  <div v-for="bookmark in bookmarks">
    <button @click="deleteBookmark(bookmark.url)">X</button>　
    <a :href="bookmark.url" target="_blank">
      {{ bookmark.name }}
    </a>
  </div>
</template>

<script setup>
  import { ref, onMounted } from 'vue';
  import axios from 'axios';

  const bookmarks = ref({});
  const inputUrl = ref('');
  const inputName = ref('');

  onMounted(() => {
    const storge = localStorage.getItem('bookmarks');
    if(storge){
      bookmarks.value = JSON.parse(storge);
    }
  });
  const add = async () => {
    console.log('inputName', inputName.value);
    console.log('inputUrl', inputUrl.value);
    let name = '';
    try {
      // 如果可以，自動抓取標題 
      const response = await axios.get(inputUrl.value);
      const html = response.data;
      const titleRegex = /<title>([^<]*)<\/title>/i;
      const match = html.match(titleRegex);
      if (match && match[1]) {
        console.log(match[1]);
        name = match[1]
      } else {
        name = inputUrl.value
      }       
    }
    catch (e){
      name = inputUrl.value
    }
    bookmarks.value[inputUrl.value] = {
      "url": inputUrl.value,
      "name": name
    }
    updateStorge();
    console.log(bookmarks.value);
    inputUrl.value = '';
  }
  const deleteBookmark = (url) => {
    delete bookmarks.value[url];
    updateStorge();
  }

  const deleteAll = () =>{
    bookmarks.value = {};
    updateStorge();
  }

  const updateStorge = () => {
    localStorage.setItem('bookmarks', JSON.stringify(bookmarks.value));
  }
  
</script>



<style scoped>

</style>

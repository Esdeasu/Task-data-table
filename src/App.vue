<script setup>
import MainTable from "./components/MainTable.vue";
import {ref, onMounted, computed} from "vue"

const data = ref([]); //Тело таблицы
const headArr = ref([]) //Заголовки таблицы

//Считает максимальный номер атрибута, т.к. номера идут по порялдку от 1 до maxAttrId
const maxAttrId = computed(() => {
  return data.value.reduce((acc, obj) => {
    const attrIds = obj.tag_attributes.map(attr => attr.attribute_id);
    const maxAttrId = Math.max(...attrIds);
    return Math.max(acc, maxAttrId);
  }, 0)
})

//Формирует массив массивов которые состоят из строк для вывода в тело таблицы
const bodyArrays = computed(()=> {
  return data.value.map(obj => {
    const attributes = new Array(maxAttrId.value).fill("").map((value, index) => {
      const attribute = obj.tag_attributes.find(attr => attr.attribute_id === index + 1);
      return attribute ? attribute.attribute_value : "";
    });
    return [obj.tag_id, obj.tag_name, ...attributes];
  }).map(arr => arr.map(val => val.toString()))
})

//Формирует массив строк для вывода в header таблицы в зависимости от maxAttrId
function headerArr() {
  let tagArray = ["Id тега", "Имя тега"];
  for (let i = 1; i <= maxAttrId.value; i++) {
      tagArray.push("Атрибут " + i);
  }
  headArr.value = tagArray
}
onMounted(()=>{
  //Получени данных из файла
  fetch('../src/data.json')
    .then((response) => response.json())
    .then((json) => {
      data.value = [...json.result]
      headerArr()
    });
    
})
</script>

<template>
  <main-table 
    class="flex justify-self-center"
    :head-array="headArr" 
    :body-array="bodyArrays"
    :default-tag="'Id тега'"
  />
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>

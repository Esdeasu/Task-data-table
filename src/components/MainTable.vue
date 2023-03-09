
<template>
  <div class="flex-col p-20">
    <div class="h-14">
      <input 
        type="text"
        class="border-2 mb-2 h-10 p-2"
        placeholder="Search records"
        v-model="filterValue"
      />
    </div>
    <table>
      <thead>
        <tr>
          <th
            v-for="(column, index) in headArray"
            v-bind:key="index"
            @click="sort(column)"
            class="whitespace-nowrap border-2 p-2 text-left cursor-pointer"
          >
            {{column}}
          </th>
        </tr>
      </thead>
      <tbody>
        <tr
          v-for="(row, index) in sortedArray"
          v-bind:key="index"
        >
          <td
            v-for="(rowItem, itemIndex) in row"
            v-bind:key="itemIndex"
            class="border-2 p-2"
          >
            {{rowItem}}
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  name: 'Table',
  props:{
    headArray: Array, //Заголовки таблицы
    bodyArray: Array, //Тело таблицы
    defaultTag: String, //Столбец для сортировки по умолчанию
  },
  data () {
    return {
      filterValue:"", //Фильтр
      sortDirection: 1, //Направление сортировки 
      sortBy: '', //Выбранный для сортировки столбец
    }
  },
  computed: {
    //Фильтрация тела таблицы по имени тега
    filterArray(){
      return this.bodyArray.filter((ticker) =>
      ticker[1].toLowerCase().includes(this.filterValue.toLowerCase())
      );
    },
    //Сортировка тела таблицы по sortBy и sortDirection
    sortedArray() {
      const type = this.sortBy === 'Id тега' ? 'Number' : 'String'
      const direction = this.sortDirection
      const head = this.headArray.indexOf(this.sortBy);

      return [
        ...this.filterByFlag(head).sort(this.sortMethods(type, head, direction)),
        ...this.filterByFlag(head, true)
      ]
    
    }
  },
  methods:{
    //Выделение строки с пустым атрибутом столбца sortBy либо заполненным
    filterByFlag(head, empty = false){
      return this.filterArray.filter((ticker) =>
        empty? !ticker[head] : ticker[head]
      );
    },
    //Фукция переключения направления и столбца сортировки
    sort(head) {
      if (this.sortBy === head) {
        if (this.sortDirection === -1) {
          this.sortBy = this.defaultTag
        } 
        this.sortDirection *= -1
      } else {
        this.sortBy = head
        this.sortDirection = 1
      }
    },
    //Функция сортировки
    sortMethods(type, head, direction) {
       switch (type) {
          case 'String': {
            return direction === 1 ?
              (a, b) => b[head] > a[head] ? -1 : a[head] > b[head] ? 1 : 0 :
              (a, b) => a[head] > b[head] ? -1 : b[head] > a[head] ? 1 : 0 
          }
          case 'Number': {
            return direction === -1 ?
              (a, b) => Number(b[head]) - Number(a[head]) :
              (a, b) => Number(a[head]) - Number(b[head])
          } 
       }
    }
  },
  mounted () {
    //Опредение столбца сортировки по умолчанию
    this.sortBy = this.defaultTag;
  }
}
</script>
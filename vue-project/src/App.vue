<template>
  <div class="main_content">
    <div class="div_main">
      <Conpont @date_value="int($event.id, $event.categor)" />
      <texaline @textaria_value ='aria' /> 
      <a-button @click="go">Добавить задачу</a-button>
    </div>
  </div>
  <div class="Tabl">
    <tab :tasks="tasks" :categories="categories" />
  </div>
</template>

<script setup>
import Conpont from "./components/conpont.vue";
import texaline from './components/textaline.vue'
import tab from "./components/tab.vue";
import { ref, onMounted } from 'vue';

let tasks = ref([
   
]);

let categories = ref([])
onMounted(() => {
  loadData();
});

function loadData() {
  if (localStorage.getItem('tasks')) {
    tasks.value = JSON.parse(localStorage.getItem('tasks'));
  }
  if (localStorage.getItem('categories')) {
    categories.value = JSON.parse(localStorage.getItem('categories'));
  }
}

function saveData() {
  localStorage.setItem('tasks', JSON.stringify(tasks.value));
  localStorage.setItem('categories', JSON.stringify(categories.value));
}

var goop;
var value_comp;
var ari_text;

function aria(value) {
  ari_text = value;
}

function int(id, categor) {
  goop = id;
  categories.value.push({
    name: categor.name,
    id: categor.id
  });
  console.log(categories.value);
}

function go() {
  let maxId = tasks.value.reduce((max, task) => {
    return task.id > max ? task.id : max;
  }, 0);

  tasks.value.push({
    id: ++maxId, 
    text: ari_text,
    categoryId: goop,
    done: false
  });
  saveData();
}
</script>


 <style scoped>
 .Tabl{
 width: 50%;
 margin: 5% auto;
  
 
 }
.main_content {
  
  display: flex;
  align-items: center;
  align-content:center ;
  justify-content: center; 
   align-items: center;
  height: 25vh; 

}
.div_main{
  margin-top: 150px;
  background-color: rgb(149, 149, 149);
  width: 1000px;
  border-radius: 0.5rem;
  display: flex;
  justify-content: space-around;
  padding:  20px ;
}

</style>
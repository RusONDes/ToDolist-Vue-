<template>
  <a-select
    v-model:value="value"
    placeholder="Выбирите категорию или создайте ее"
    style="width: 300px"
    :options="items.map(item => ({ value: item }))"
    @change="emitSelectedValue"
  >
    <template #dropdownRender="{ menuNode: menu }">
      <v-nodes :vnodes="menu" />
      <a-divider style="margin: 4px 0" />
      <a-space style="padding: 4px 8px">
        <a-input ref="inputRef" v-model:value="name" placeholder="Создать категорию" />
        <a-button type="text" @click="addItem">
          <template #icon>
            <plus-outlined />
          </template>
          Добавить категорию
        </a-button>
      </a-space>

      
    </template>
    
    
  </a-select>
 
</template>


<script setup lang="ts">
import { ref, defineComponent, watchEffect, defineEmits ,onMounted } from 'vue';
import { PlusOutlined  } from '@ant-design/icons-vue';

const VNodes = defineComponent({
  props: {
    vnodes: {
      type: Object,
      required: true,
    },
  },
  render() {
    return this.vnodes;
  },
});

const emits = defineEmits();

let categories = ref([]);

const items = ref<string[]>([]);

watchEffect(() => {
  items.value = categories.value.map(category => category.name);
});

const value = ref();
const inputRef = ref();
const name = ref();

const emitSelectedValue = () => {
  const selectedItem = categories.value.find(item => item.name === value.value);
  if (selectedItem) {
    emits('date_value', { categor: selectedItem, id: selectedItem.id });
  }
};

const addItem = () => {
  let maxId = categories.value.reduce((max, task) => {
    return task.id > max ? task.id : max;
  }, 0);
  const newCategory = {
    name: name.value || `new category ${maxId++}`,
    id: maxId + 2
  };

  categories.value.push(newCategory);
  name.value = '';

  saveData();
};

function saveData() {
  localStorage.setItem('categories', JSON.stringify(categories.value));
}

// Load data from localStorage on component mount
onMounted(() => {
  const savedCategories = localStorage.getItem('categories');
  if (savedCategories) {
    categories.value = JSON.parse(savedCategories);
  }
});
</script>

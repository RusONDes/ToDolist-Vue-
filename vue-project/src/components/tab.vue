<template>
  <a-table v-if="props.categories && props.tasks" :columns="columns" :row-selection="rowSelection" :data-source="transformedData" row-key="id" />
</template>

<script setup>
import { ref, computed, watchEffect, defineProps } from 'vue';

const props = defineProps({
  tasks: Array,
  categories: Array,
});

const columns = ref([
  {
    title: 'Category',
    dataIndex: 'name',
    key: 'categoryId',
    filters: [],
    onFilter: (value, record) => record.categoryId === value,
    width: '15%',
  },
  {
    title: 'Status',
    dataIndex: 'text',
    key: 'status',
  },
]);

const transformedData = ref([]);

// Update the columns filters whenever props.categories changes
watchEffect(() => {
  if (props.categories) {
    columns.value[0].filters = props.categories.map(cat => ({ text: cat.name, value: cat.id }));
  }
});

// Пересчет transformedData при изменении props.categories и props.tasks
watchEffect(() => {
  if (props.categories && props.tasks) {
    transformedData.value = props.tasks.map(task => {
      const category = props.categories.find(cat => cat.id === task.categoryId);
      return {
        ...task,
        name: category ? category.name : 'Unknown Category',
        done: task.done ? 'Done' : 'Not Done',
      };
    });
  }
});

const rowSelection = {
  onChange: (selectedRowKeys, selectedRows) => {
    console.log(`selectedRowKeys: ${selectedRowKeys}`, 'selectedRows: ', selectedRows);
  },
  getCheckboxProps: record => ({
    disabled: record.name === 'Category',
  }),
};
</script>

<style scoped>
.ant-table-row-selected {
  background-color: #e6f7ff;
}
</style>

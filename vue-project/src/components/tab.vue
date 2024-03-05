<template>
  <a-table v-if="props.categories && props.tasks" :columns="columns" :row-selection="rowSelection" :data-source="transformedData" row-key="id">
    <template #bodyCell="{ column, text, record }">
      <template v-if="column.dataIndex === 'operation'">
        <div class="editable-row-operations">
          <span v-if="editableData[record.id]">
            <a-typography-link @click="save(record.id)">Save</a-typography-link>
            <a-popconfirm title="Sure to cancel?" @confirm="cancel(record.id)">
              <a>Cancel</a>
            </a-popconfirm>
          </span>
          <span v-else>
            <a @click="edit(record.id)">Edit</a>
          </span>
          <a-popconfirm
            v-if="transformedData.length"
            title="Sure to delete?"
            @confirm="onDelete(record.id)"
          >
            <a>Delete</a>
          </a-popconfirm>
        </div>
      </template>
      <template v-else>
        <span v-if="editableData[record.id]">
          <a-input  v-if="editableData[record.id]"
            v-model:value="editableData[record.id][column.dataIndex]"
            style="margin: -5px 0"
             />
        </span>
        <template v-else>
          {{ text }}
        </template>
      </template>
    </template>
  </a-table>
</template>

<script setup>
import { cloneDeep } from 'lodash-es';
import { ref, watchEffect, defineProps, defineEmits, reactive , } from 'vue';

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
  {
    title: 'Operation',
    dataIndex: 'operation',
  },
]);

const transformedData = ref([]);

// Update the columns filters whenever props.categories changes
watchEffect(() => {
  if (props.categories) {
    columns.value[0].filters = props.categories.map(cat => ({ text: cat.name, value: cat.id }));
  }
});

// Recalculate transformedData when props.categories and props.tasks change
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

const emits = defineEmits(['itemDeleted']);


const editableData = reactive({});

const edit = id => {
  editableData[id] = cloneDeep(transformedData.value.filter(item => id === item.id)[0]);
};

const save = id => {
  const index = transformedData.value.findIndex(item => item.id === id);
  if (index !== -1) {
    Object.assign(transformedData.value[index], editableData[id]);
    const modifiedItem = cloneDeep(editableData[id]);
    delete editableData[id];
    // Создаем глубокую копию измененного элемента
    emits('itemSaved', modifiedItem);
    console.log(modifiedItem)
  }

  
};

const cancel = id => {
  delete editableData[id];
};

const onDelete = id => {
  transformedData.value = transformedData.value.filter(item => item.id !== id);
  emits('itemDeleted', id);
};
</script>

<style scoped>
.editable-row-operations a {
  margin-right: 8px;
}
</style>

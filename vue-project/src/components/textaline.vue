<template>
  <a-auto-complete
    v-model:value="value"
    :options="options"
    style="width: 300px"
    @change="emitSelectedValue"
    @search="handleSearch"
    @select="onSelect"
  >
    <a-textarea
    placeholder="Что хотите обсудить"
  class="custom"
  style="height: 30px; max-height: 150px; "

    />
  </a-auto-complete>
</template>
<script lang="ts" setup>
import { ref } from 'vue';
const selectedCategory = ref('');
const emits = defineEmits();

const value = ref('');

const options = ref<{ value: string }[]>([]);
const onSelect = (value: string) => {
  console.log('onSelect', value);
  selectedCategory.value = value;
};


const handleSearch = (value: string) => {
  options.value = !value
    ? []
    : [{ value }, { value: value + value }, { value: value + value + value }];
};
const emitSelectedValue = () => {
  emits('textaria_value', value.value);
};
</script>
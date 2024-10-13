<template>
  <div>
    <label>
      <input type="checkbox" v-model="allowEmpty" @change="fetchRubrics" />
      Отображать пустые рубрики
    </label>
    <div>Выбранные рубрики: {{ selectedCount }}</div>
    <ul>
      <RubricNode
        v-for="rubric in rubrics"
        :key="rubric.id"
        :rubric="rubric"
        @toggle="toggleSelect"
      />
    </ul>
  </div>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue';
import RubricNode from './RubricNode.vue'; 

const rubrics = ref([]);
const allowEmpty = ref(false);
const selectedRubrics = ref(new Set());

const fetchRubrics = async () => {
  const allowEmptyParam = allowEmpty.value ? '?allowEmpty=1' : '';
  const response = await fetch(
    `https://www.klerk.ru/index.php/v3/event/rubrics${allowEmptyParam}`
  );
  rubrics.value = await response.json();
  console.log(rubrics.value);
};

const selectedCount = computed(() => {
  let total = 0;
  selectedRubrics.value.forEach((rubric) => {
    total += calculateCount(rubric);
  });
  return total;
});

const calculateCount = (rubric) => {
  let total = rubric.count || 0;
  if (rubric.children) {
    rubric.children.forEach((child) => {
      total += calculateCount(child);
    });
  }
  return total;
};

const toggleSelect = (rubric) => {
  if (selectedRubrics.value.has(rubric)) {
    selectedRubrics.value.delete(rubric);
  } else {
    selectedRubrics.value.add(rubric);
  }
};

onMounted(() => {
  fetchRubrics();
});
</script>

<style></style>

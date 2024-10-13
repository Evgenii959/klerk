<template>
  <li>
    <div>
      <input type="checkbox" @change="toggle" />
      <a :href="`https://www.klerk.ru${rubric.url}`">{{ rubric.title }}</a>
      ({{ rubric.count }} / {{ totalCount }})
      <button @click="toggleCollapse">
        {{ isCollapsed ? 'Развернуть' : 'Свернуть' }}
      </button>
    </div>
    <ul v-if="rubric.children && !isCollapsed">
      <RubricNode
        v-for="child in rubric.children"
        :key="child.id"
        :rubric="child"
        @toggle="toggleSelect"
      />
    </ul>
  </li>
</template>

<script setup>
import { ref, computed } from 'vue';

const props = defineProps({
  rubric: Object,
});

const emit = defineEmits(['toggle']);

const isCollapsed = ref(false);

const totalCount = computed(() => {
  let total = props.rubric.count || 0;
  if (props.rubric.children) {
    props.rubric.children.forEach((child) => {
      total += calculateCount(child);
    });
  }
  return total;
});

const toggleCollapse = () => {
  isCollapsed.value = !isCollapsed.value;
};

const toggle = () => {
  emit('toggle', props.rubric);
};

const calculateCount = (rubric) => {
  let total = rubric.count || 0;
  if (rubric.children) {
    rubric.children.forEach((child) => {
      total += calculateCount(child);
    });
  }
  return total;
};
</script>
<style></style>

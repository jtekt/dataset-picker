<template>
  <v-row v-for="(_, index) in filters" :key="index" dense>
    <v-col>
      <v-combobox
        :items="unusedFields"
        v-model="filters[index].key"
        label="Key"
      />
    </v-col>
    <v-col>
      <v-text-field v-model="filters[index].value" label="Value" />
    </v-col>
    <v-col cols="auto">
      <v-btn @click="deleteFilter(index)" icon="mdi-close" variant="plain" />
    </v-col>
  </v-row>
  <v-row dense>
    <v-col>
      <v-btn
        @click="addFilter()"
        prepend-icon="mdi-filter-plus"
        color="primary"
      >
        Add filter
      </v-btn>
    </v-col>
  </v-row>
</template>

<script setup lang="ts">
import { ref, computed, watch } from "vue";

interface Filter {
  key: string;
  value: string;
}

const props = defineProps<{ fields: any[] | undefined }>();

const emit = defineEmits(["update"]);
const filters = ref<Filter[]>([]);

const addFilter = () => {
  filters.value.push({ key: "", value: "" });
};

const deleteFilter = (index: number) => {
  filters.value.splice(index, 1);
};

const convertToObject = () =>
  filters.value.reduce(
    (prev: any, { key, value }: Filter) => ({ ...prev, [key]: value }),
    {}
  );

const unusedFields = computed(() => {
  if (!props.fields) return [];
  return props.fields.filter(
    (field) => !filters.value.map((filter) => filter.key).includes(field)
  );
});

watch(filters.value, () => {
  emit("update", convertToObject());
});
</script>

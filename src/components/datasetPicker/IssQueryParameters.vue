<template>
  <div>
    <v-row v-for="(param, index) in params" :key="index" dense>
      <v-col>
        <v-combobox :items="fields" v-model="param.key" label="label" />
      </v-col>
      <v-col>
        <v-text-field label="value" v-model="param.value" />
      </v-col>
      <v-col cols="auto" @click="removeFilter(index)">
        <v-btn icon="mdi-delete" variant="plain" />
      </v-col>
    </v-row>
    <v-row dense>
      <v-col cols="auto">
        <v-btn color="primary" prepend-icon="mdi-plus" @click="addFilter()">
          Add filter
        </v-btn>
      </v-col>
    </v-row>
  </div>
</template>

<script setup lang="ts">
import { ref, watch, onMounted } from "vue";
import axios from "axios";

const { VITE_IMAGE_STORAGE_URL } = import.meta.env;

const emit = defineEmits(["update"]);

interface Field {
  key: string;
  value: string;
}

const params = ref<Field[]>([]);
const fields = ref<string[]>([]);
// TODO: get fields

watch(params.value, () => {
  emit("update", params.value);
});
function addFilter() {
  params.value.push({ key: "", value: "" });
}

function removeFilter(index: number) {
  params.value.splice(index, 1);
}

onMounted(async () => {
  const url = `${VITE_IMAGE_STORAGE_URL}/fields`;
  const { data } = await axios.get(url);
  fields.value = data;
});
</script>

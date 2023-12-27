<template>
  <!-- TODO: make a component -->
  <h3 class="mb-6">Query parameters</h3>
  <v-row dense>
    <v-col>
      <v-text-field v-model="issUrl" label="ISS URL" />
    </v-col>
    <v-col cols="auto">
      <v-btn
        class="mt-2"
        color="primary"
        prepend-icon="mdi-check"
        @click="getFields()"
        :loading="fieldsLoading"
      >
        Select
      </v-btn>
    </v-col>
  </v-row>
  <v-row dense>
    <v-col>
      <IsoDatePicker label="From" v-model="dateFilters.from" />
    </v-col>
    <v-col>
      <IsoDatePicker label="To" v-model="dateFilters.to" />
    </v-col>
  </v-row>
  <v-row dense>
    <v-col>
      <v-text-field label="Limit" v-model.number="limit" type="number" />
    </v-col>
  </v-row>
  <QueryFilters @update="filters = $event" :fields="fields" />

  <h3 class="my-6">Dataset preview</h3>
  <!-- TODO: consider a carrousel preview -->
  <DatasetPreview
    :query-parameters="{ ...filters, ...dateFilters, limit }"
    :issUrl="issUrl"
    @dataPreviewed="getFields()"
  />

  <!-- TODO: make a component -->
  <h3 class="my-6">Classes preview</h3>

  <v-row>
    <v-col>
      <v-select :items="fields" v-model="field" label="Class field" />
    </v-col>
    <v-col cols="auto">
      <v-btn
        class="mt-2"
        color="primary"
        @click="getClassNames()"
        :loading="classesLoading"
        prepend-icon="mdi-magnify"
      >
        Get classes
      </v-btn>
    </v-col>
  </v-row>

  <v-row v-if="field && classNames.length">
    <v-col>
      <div v-for="className in classNames" :key="className">
        <IssClassPreview
          :className="className"
          :field="field"
          :query-parameters="{ ...filters, ...dateFilters, limit }"
          :issUrl="issUrl"
        />
      </div>
    </v-col>
  </v-row>

  <v-row v-if="field" justify="center">
    <v-col cols="auto">
      <v-btn
        color="primary"
        prepend-icon="mdi-download"
        @click="startImportDatasetTask()"
        :loading="loading"
      >
        Import dataset
      </v-btn>
    </v-col>
  </v-row>
</template>

<script lang="ts" setup>
import QueryFilters from "./QueryFilters.vue";
import IsoDatePicker from "./IsoDatePicker.vue";
import DatasetPreview from "./DatasetPreview.vue";
import IssClassPreview from "./IssClassPreview.vue";
import { ref } from "vue";
import axios from "axios";

// Two options: either get all images and separate them in classes in the GUI
// or make one GET request for each class
// Currently going for the second option

const { VITE_IMAGE_STORAGE_URL } = import.meta.env;

const emit = defineEmits(["import"]);

const issUrl = ref(VITE_IMAGE_STORAGE_URL);
const fields = ref<string[]>();
const field = ref<string>();
const fieldsLoading = ref(false);
const classNames = ref<string[]>([]);
const loading = ref(false);
const classesLoading = ref(false);
const dateFilters = ref<any>({
  from: "",
  to: "",
});
const filters = ref<any>({});
const limit = ref(100);

const getFields = async () => {
  try {
    fieldsLoading.value = true;
    const url = `${issUrl.value}/fields`;
    const { data } = await axios.get(url);
    fields.value = data;
  } catch (error) {
    alert("failed to get fields");
    console.error(error);
  } finally {
    fieldsLoading.value = false;
  }
};

const getClassNames = async () => {
  // Needed to trigger mounted in previews
  classNames.value = [];
  classesLoading.value = true;
  try {
    const url = `${issUrl.value}/fields/${field.value}`;
    const { data } = await axios.get(url);
    classNames.value = data;
  } catch (error) {
    alert(error);
  } finally {
    classesLoading.value = false;
  }
};

const importDataset = async () => {
  const queryParams = {
    ...filters.value,
    ...dateFilters.value,
    limit: limit.value,
  };

  const body = {
    issUrl: issUrl.value,
    classField: field.value,
    classes: classNames.value,
    queryParams,
  };
  loading.value = true;
  try {
    const { data } = await axios.put(`/dataset`, body);
    emit("import");
    // store.setDataset(data.classes);
  } catch (error) {
    alert(error);
    console.error(error);
  } finally {
    loading.value = false;
  }
};

const startImportDatasetTask = async () => {
  const queryParams = {
    ...filters.value,
    ...dateFilters.value,
    limit: limit.value,
  };

  const body = {
    imageStorageApiUrl: issUrl.value,
    classField: field.value,
    classes: classNames.value,
    queryParams,
  };
  try {
    await axios.post(`/tasks/datasetimport`, body);
    emit("import");
  } catch (error) {
    alert(error);
    console.error(error);
  }
};
</script>

<style scoped>
.wrapper {
  max-height: 60vh;
  overflow-y: auto;
}
</style>

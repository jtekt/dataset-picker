<template>
  <v-expansion-panels multiple v-model="panels">
    <v-expansion-panel title="Query parameters">
      <v-expansion-panel-text>
        <v-row dense>
          <v-col>
            <v-text-field v-model="issUrlUserInput" label="ISS URL" />
          </v-col>
          <v-col cols="auto">
            <v-btn
              class="mt-2"
              color="primary"
              prepend-icon="mdi-check"
              @click="selectIss()"
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
      </v-expansion-panel-text>
    </v-expansion-panel>

    <v-expansion-panel title="Dataset preview">
      <v-expansion-panel-text>
        <!-- TODO: consider a carrousel preview -->
        <DatasetPreview
          :query-parameters="{ ...filters, ...dateFilters, limit }"
          :issUrl="issUrl"
          @dataPreviewed="getFields()"
          @itemClicked="$emit('datasetPreviewItemClicked', $event)"
        />
      </v-expansion-panel-text>
    </v-expansion-panel>

    <v-expansion-panel title="Classes" v-if="fields.length">
      <v-expansion-panel-text>
        <ClassesPreview
          :filters="filters"
          :iss-url="issUrl"
          :dateFilters="dateFilters"
          :limit="limit"
          :fields="fields"
          :hideClassesPreview="hideClassesPreview"
          v-model:field="field"
          v-model:classNames="classNames"
        />
      </v-expansion-panel-text>
    </v-expansion-panel>
  </v-expansion-panels>

  <v-row v-if="field" justify="center" class="mt-4">
    <v-col cols="auto">
      <v-btn
        color="primary"
        prepend-icon="mdi-download"
        @click="selectDataset()"
      >
        select dataset
      </v-btn>
    </v-col>
  </v-row>
</template>

<script lang="ts" setup>
import QueryFilters from "./QueryFilters.vue";
import IsoDatePicker from "./IsoDatePicker.vue";
import DatasetPreview from "./DatasetPreview.vue";
import ClassesPreview from "./ClassesPreview.vue";
import { ref } from "vue";
import axios from "axios";

// Two options: either get all images and separate them in classes in the GUI
// or make one GET request for each class
// Currently going for the second option

const props = defineProps<{
  defaultIssUrl?: string;
  hideClassesPreview?: boolean;
}>();

const emit = defineEmits(["select", "datasetPreviewItemClicked"]);
const issUrlUserInput = ref(props.defaultIssUrl);
const issUrl = ref(props.defaultIssUrl);
const fields = ref<string[]>([]);
const field = ref<string>();
const classNames = ref<string[]>([]);
const fieldsLoading = ref(false);
const panels = ref([0, 1, 2]);
const dateFilters = ref<{ from: string; to: string }>({
  from: "",
  to: "",
});
const filters = ref<any>({});
const limit = ref(100);

const selectIss = () => {
  issUrl.value = issUrlUserInput.value;
  getFields();
};

const getFields = async () => {
  try {
    fields.value = [];
    fieldsLoading.value = true;
    const url = `${issUrl.value}/fields`;
    const { data } = await axios.get(url);
    fields.value = data;
  } catch (error) {
    console.error(error);
  } finally {
    fieldsLoading.value = false;
  }
};

const selectDataset = () => {
  const queryParams = {
    ...filters.value,
    ...dateFilters.value,
    limit: limit.value,
  };

  const eventData = {
    queryParams,
    imageStorageApiUrl: issUrl.value,
    classField: field.value,
    classes: classNames.value,
  };
  emit("select", eventData);
};
</script>

<style scoped>
.wrapper {
  max-height: 60vh;
  overflow-y: auto;
}
</style>

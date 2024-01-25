<template>
  <v-app>
    <v-main>
      <v-container>
        <v-row>
          <v-col>
            <v-text-field v-model.number="dataset.queryParams.limit" />
          </v-col>
        </v-row>
        <v-row>
          <v-col>
            <v-text-field
              v-model="dataset.imageStorageApiUrl"
              label="ISS URL"
            ></v-text-field>
          </v-col>
        </v-row>
        <v-row>
          <v-col>
            <v-card>
              <v-card-title> Dataset picker </v-card-title>
              <v-card-text>
                <DatasetPicker
                  @datasetPreviewItemClicked="handleDatasetPreviewItemClicked"
                  v-model="dataset"
                />
              </v-card-text>
            </v-card>
          </v-col>
        </v-row>
        <v-row>
          <v-col>
            {{ dataset }}
          </v-col>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script setup lang="ts">
import DatasetPicker from "@/components/datasetPicker/IssDatasetPicker.vue";
import { ref } from "vue";

const { VITE_IMAGE_STORAGE_URL } = import.meta.env;
const dataset = ref({
  imageStorageApiUrl: VITE_IMAGE_STORAGE_URL,
  queryParams: {
    limit: 123,
  },
});

function handleDatasetPreviewItemClicked(image: any) {
  alert(JSON.stringify(image));
}
</script>

<template>
  <v-app>
    <v-main>
      <v-container>
        <v-card>
          <v-card-title> Dataset picker </v-card-title>
          <v-card-text>
            <DatasetPicker
              :defaultIssUrl="VITE_IMAGE_STORAGE_URL"
              @select="handleDatasetSelection"
            />
          </v-card-text>
        </v-card>
      </v-container>
    </v-main>
  </v-app>
</template>

<script setup lang="ts">
import DatasetPicker from "@/components/datasetPicker/DatasetIssImport.vue";
const { VITE_IMAGE_STORAGE_URL } = import.meta.env;
function handleDatasetSelection(queryparameters: any) {
  alert(JSON.stringify(queryparameters));
}
</script>

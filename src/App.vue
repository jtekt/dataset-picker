<template>
  <v-app>
    <v-main>
      <v-container>
        <v-row>
          <v-col>
            <v-card>
              <v-card-title> Dataset picker </v-card-title>
              <v-card-text>
                <DatasetPicker
                  :defaultIssUrl="VITE_IMAGE_STORAGE_URL"
                  hideClassesPreview
                  @select="handleDatasetSelection"
                  @datasetPreviewItemClicked="handleDatasetPreviewItemClicked"
                />
              </v-card-text>
            </v-card>
          </v-col>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script setup lang="ts">
import DatasetPicker from "@/components/datasetPicker/IssDatasetPicker.vue";
const { VITE_IMAGE_STORAGE_URL } = import.meta.env;
function handleDatasetSelection(queryparameters: any) {
  alert(JSON.stringify(queryparameters));
}

function handleDatasetPreviewItemClicked(image: any) {
  alert(JSON.stringify(image));
}
</script>

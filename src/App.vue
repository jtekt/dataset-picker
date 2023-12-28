<template>
  <v-app>
    <v-main>
      <v-container>
        <v-card>
          <v-card-title> Dataset picker </v-card-title>
          <v-card-text>
            <DatasetPicker />
          </v-card-text>
        </v-card>
      </v-container>
    </v-main>
  </v-app>
</template>

<script setup lang="ts">
import DatasetPicker from "@/components/datasetPicker/DatasetIssImport.vue";
</script>

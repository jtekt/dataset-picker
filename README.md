# Dataset picker

A Vue.js component to select a dataset from the JTEKT [Image storage microservice](https://github.com/jtekt/image-storage-microservice).

## Usage example

```vue
<template>
  <v-app>
    <DatasetPicker v-model="dataset" />
  </v-app>
</template>

<script setup lang="ts">
import DatasetPicker from "@jtekt/dataset-picker"
import { ref } from "vue"

const { VITE_IMAGE_STORAGE_URL } = import.meta.env
const dataset = ref({
  imageStorageApiUrl: VITE_IMAGE_STORAGE_URL,
  queryParams: {
    limit: 123,
  },
})
</script>
```

## Development

### Project setup

```
npm install
```

### Compiles and hot-reloads for development

```
npm run dev
```

### Compiles and minifies for production

```
npm run build
```

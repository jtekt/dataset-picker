<template>
  <v-row align="center">
    <v-col cols="auto">
      <v-btn
        color="primary"
        @click="queryDataset()"
        :loading="loading"
        prepend-icon="mdi-magnify"
      >
        Query
      </v-btn>
    </v-col>
    <v-spacer />
    <v-col cols="auto">
      <v-icon>mdi-image-multiple</v-icon> x
      {{ Math.min(imageCount, queryParameters.limit) }}
    </v-col>
  </v-row>
  <v-row v-if="datasetLoadError" justify="center">
    <v-col cols="auto" class="text-error"> Failed to get dataset </v-col>
  </v-row>
  <div v-else class="table_wrapper mt-4">
    <!-- TODO: This could be a v-data-table -->
    <v-table fixed-header>
      <thead>
        <tr>
          <th></th>
          <th class="text-left">Time</th>
          <th class="text-left" v-for="field in fields" :key="field">
            {{ field }}
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="image in images" :key="image._id">
          <td class="image-property">
            <v-img
              :src="getImageSrc(image._id)"
              width="5rem"
              @click="emit('itemClicked', image)"
            />
          </td>
          <td>{{ image.time }}</td>
          <td
            v-for="field in fields"
            :key="`${image._id}_${field}`"
            class="image-property"
          >
            <span class="">{{ image.data[field] }}</span>
          </td>
        </tr>
      </tbody>
    </v-table>
  </div>

  <!-- TODO: pagination -->
</template>

<script setup lang="ts">
import { ref, watch, onMounted } from "vue";
import axios from "axios";

interface Image {
  _id: string;
  data: any;
  time: string;
}

const props = defineProps<{
  queryParameters: any;
  issUrl?: string;
}>();

const emit = defineEmits(["dataPreviewed", "itemClicked"]);
const loading = ref(false);
const images = ref<Image[]>([]);
const imageCount = ref(0);
const fields = ref<string[]>([]);
const datasetLoadError = ref(false);
watch(
  () => props.issUrl,
  () => {
    images.value = [];
    imageCount.value = 0;

    queryDataset();
  }
);

const queryDataset = async () => {
  if (!props.issUrl) return;

  datasetLoadError.value = false;
  loading.value = true;

  const url = `${props.issUrl}/images`;
  const limit = 20;
  const params = {
    ...props.queryParameters,
    limit,
  };

  try {
    const {
      data: { items, total },
    } = await axios.get(url, { params });
    images.value = items;
    imageCount.value = total;

    emit("dataPreviewed");
  } catch (error) {
    console.error(error);
    datasetLoadError.value = true;
  } finally {
    loading.value = false;
  }
};

const getFields = async () => {
  if (!props.issUrl) return;
  const url = `${props.issUrl}/fields`;
  const { data } = await axios.get(url);
  fields.value = data;
};

onMounted(() => {
  getFields();
  queryDataset();
});

const getImageSrc = (_id: string) => `${props.issUrl}/images/${_id}/image`;

// const datasetGuiUrl = computed(() => {
//   const url = new URL(`${VITE_IMAGE_STORAGE_GUI_URL}/images/`);
//   for (const key in props.queryParameters) {
//     url.searchParams.set(key, props.queryParameters[key]);
//   }

//   return url.toString();
// });
</script>

<style scoped>
.table_wrapper {
  max-height: 50vh;
  overflow-y: auto;
}

td {
  white-space: nowrap;
  text-overflow: ellipsis;
  max-width: 30ch;
  overflow: hidden;
}
</style>

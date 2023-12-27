<template>
  <v-card variant="flat">
    <!-- <v-card-text> -->
    <v-carousel height="200" hide-delimiters v-if="images.length">
      <v-carousel-item
        v-for="image in images.slice(0, 50)"
        :key="image._id"
        :src="getImageSrc(image)"
        contain
      />
    </v-carousel>
    <!-- </v-card-text> -->
    <v-card-title>
      {{ className }}
    </v-card-title>
    <v-card-subtitle>
      <v-icon>mdi-image</v-icon> x {{ images.length }}
    </v-card-subtitle>
  </v-card>
</template>

<script lang="ts" setup>
import { ref, onMounted } from "vue";
import axios from "axios";

interface Image {
  _id: string;
}

const props = defineProps<{
  className: string;
  field: string;
  issUrl: string;
  queryParameters: any;
}>();
const images = ref<Image[]>([]);

// PROBLEM: only on mounted
onMounted(async () => {
  getImages();
});

async function getImages() {
  const url = `${props.issUrl}/images`;

  const params = { ...props.queryParameters, [props.field]: props.className };
  const { data } = await axios.get(url, { params });
  images.value = data.items;
}

function getImageSrc({ _id }: Image) {
  return `${props.issUrl}/images/${_id}/image`;
}
</script>

<style scoped></style>

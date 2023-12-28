<template>
  <v-row>
    <v-col>
      <v-select :items="fields" v-model="fieldInternal" label="Class field" />
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
        <ClassPreview
          :className="className"
          :field="field"
          :query-parameters="{ ...filters, ...dateFilters, limit }"
          :issUrl="issUrl"
        />
      </div>
    </v-col>
  </v-row>
</template>

<script setup lang="ts">
import ClassPreview from "./ClassPreview.vue";
import { ref, computed, watch } from "vue";
import axios from "axios";
import {} from "vue";

const props = defineProps<{
  filters: any;
  issUrl?: string;
  dateFilters: { from: string; to: string };
  limit: number;
  fields: any[];
  field?: string;
}>();
const emit = defineEmits(["update:field"]);
const classNames = ref<string[]>([]);
const classesLoading = ref(false);

const fieldInternal = computed({
  get() {
    return props.field;
  },
  set(newVal) {
    emit("update:field", newVal);
  },
});

watch(fieldInternal, () => {
  getClassNames();
});

const getClassNames = async () => {
  if (!props.issUrl) return;
  // Needed to trigger mounted in previews
  classNames.value = [];
  classesLoading.value = true;
  try {
    const url = `${props.issUrl}/fields/${fieldInternal.value}`;
    const { data } = await axios.get(url);
    classNames.value = data;
  } catch (error) {
    alert(error);
  } finally {
    classesLoading.value = false;
  }
};
</script>

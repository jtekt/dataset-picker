<template>
  <v-row>
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
    <v-col>
      <v-select :items="fields" v-model="fieldInternal" label="Class field" />
    </v-col>
  </v-row>

  <template v-if="!hideClassesPreview">
    <v-row
      v-if="field && classNamesInternal?.length && !someClassesAreNotString"
    >
      <v-col>
        <div v-for="className in classNamesInternal" :key="className">
          <ClassPreview
            :className="className"
            :field="field"
            :query-parameters="{ ...filters, ...dateFilters, limit }"
            :issUrl="issUrl"
          />
        </div>
      </v-col>
    </v-row>
    <v-row v-if="someClassesAreNotString" justify="center">
      <v-col cols="auto" class="text-error text-h5">
        Classes are not of type string
      </v-col>
    </v-row>
  </template>
</template>

<script setup lang="ts">
import ClassPreview from "./ClassPreview.vue";
import { ref, computed, watch } from "vue";
import axios from "axios";

const props = defineProps<{
  filters: any;
  issUrl?: string;
  dateFilters: { from: string; to: string };
  limit: number;
  fields: any[];
  field?: string;
  classNames?: string[];
  hideClassesPreview?: boolean;
}>();

const emit = defineEmits(["update:field", "update:classNames"]);

const classesLoading = ref(false);

const fieldInternal = computed({
  get() {
    return props.field;
  },
  set(newVal) {
    emit("update:field", newVal);
  },
});

const classNamesInternal = computed({
  get() {
    console.log(props.classNames);
    return props.classNames;
  },
  set(newVal) {
    emit("update:classNames", newVal);
  },
});

watch(fieldInternal, () => {
  getClassNames();
});

const getClassNames = async () => {
  if (!props.issUrl) return;
  // Needed to trigger mounted in previews
  classNamesInternal.value = [];
  classesLoading.value = true;
  try {
    const url = `${props.issUrl}/fields/${fieldInternal.value}`;
    const { data } = await axios.get(url);
    classNamesInternal.value = data;
  } catch (error) {
    alert(error);
  } finally {
    classesLoading.value = false;
  }
};

const someClassesAreNotString = computed(() =>
  classNamesInternal.value?.some((n) => typeof n !== "string")
);
</script>

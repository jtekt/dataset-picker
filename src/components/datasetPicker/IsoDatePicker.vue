<template>
  <v-text-field type="date" :label="label" v-model="date" />
</template>

<script lang="ts" setup>
import { computed } from "vue";

const props = defineProps<{ label?: string; modelValue: string | undefined }>();
const emit = defineEmits(["update:modelValue"]);

const date = computed({
  get() {
    if (!props.modelValue) return "";
    const date = new Date(props.modelValue);
    const year = date.getFullYear();
    const month = (date.getMonth() + 1).toString().padStart(2, "0");
    const day = date.getDate().toString().padStart(2, "0");
    return `${year}-${month}-${day}`;
  },
  set(newVal: string) {
    if (!newVal) return emit("update:modelValue", "");
    const dateutc = new Date(
      new Date(newVal).getTime() + new Date().getTimezoneOffset() * 60000
    );
    emit("update:modelValue", dateutc.toISOString());
  },
});
</script>

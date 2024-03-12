<script setup>
import { ref, defineProps, defineEmits } from "vue";

const props = defineProps(["onNewItem"]);
const text = ref("");
const emit = defineEmits(['inFocus', 'submit']);

function handleSubmit() {
  if (text.value !== "") {
    emit("onNewItem", text.value);
    text.value = "";
  }
}
</script>

<template>
  <form action="" @submit.prevent="handleSubmit">
    <input type="text" v-model="text" />
  </form>
</template>

<style scoped></style>

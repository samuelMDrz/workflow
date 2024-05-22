<script setup>
import { Handle, Position } from "@vue-flow/core";
import { BxSolidBadgeCheck } from "@kalimahapps/vue-icons";

const props = defineProps({
  isValidTargetPos: {
    type: Function,
    required: false,
  },
  data: { type: String },
  name: { type: String },
  item: { type: String },
  status: { type: String },
  icon: { type: Boolean },
});
</script>

<script>
export default {
  inheritAttrs: false,
};
</script>

<template>
  <Handle type="source" :position="Position.Right" />
  <div
    style="
      flex-direction: column;
      text-align: center;
      color: black;
      font-family: 'Reddit Sans', sans-serif;
      overflow: hidden;
      white-space: nowrap;
      text-overflow: ellipsis;
    "
  >
    <h2 style="font-size: 0.9em; margin: 0.125em 0 0.625em 0">
      {{ data.status }}
    </h2>
    <div style="display: flex; font-size: 0.8em">
      <strong>Item:</strong>
      <div>{{ data.item }}</div>
    </div>
    <div style="display: flex; font-size: 0.8em">
      <strong>Usuário: </strong>
      <div>{{ data.name }}</div>
    </div>
    <div style="display: flex; font-size: 0.8em">
      <strong>Data: </strong>
      <div>{{ data.data }}</div>
    </div>
    <div v-if="icon">
      <BxSolidBadgeCheck />
    </div>
    <div v-else>Não Aceito</div>
  </div>
</template>

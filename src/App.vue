<script setup>
import { reactive, ref } from "vue";
import { VueFlow, useVueFlow } from "@vue-flow/core";
import CustomInput from "./CustomInput.vue";
import CustomNode from "./CustomNode.vue";
import axios from "axios";
import { watchEffect } from "vue";
import { onMounted } from "vue";
import { icon } from "@fortawesome/fontawesome-svg-core";

const { addEdges } = useVueFlow();

const token = ref(
  "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6ImNhNzE2NTIwLWEwNGItNGI5ZC1iNjQ1LTdiN2Q5OWYyNDQ5MyIsInJvbGUiOiJiNTM3NGQ2Mi1iMDRlLTQxOTAtYWI3OC0wMTc4NWIxZGY4MzgiLCJhcHBfYWNjZXNzIjp0cnVlLCJhZG1pbl9hY2Nlc3MiOnRydWUsImlhdCI6MTcxNjM4ODg5MCwiZXhwIjoxNzE2Mzg5NzkwLCJpc3MiOiJkaXJlY3R1cyJ9.04vFXzBcv8gUUbhgBxlZh7BC4PFxpPMpVT-yhkbSCm8"
);
const response = ref(null);

const getWorkFlow = () => {
  axios
    .get("http://31.220.101.47:8055/items/workflow_status/2091", {
      headers: {
        Authorization: `Bearer ${token.value}`,
      },
    })
    .then((res) => (response.value = res.data))
    .catch((err) => console.log(err));
};

onMounted(() => {
  getWorkFlow();
});

const updateNodeIcon = (nodeId, newIconStatus) => {
  const index = nodes.value.findIndex((node) => node.id === nodeId);
  if (index !== -1) {
    // Use set para atualizar o valor de icon
    set(nodes.value[index], "icon", newIconStatus);
  }
};
watchEffect(() => {
  if (response.value !== null) {
    nodes.value[0].data.data = response.value.data.data_criacao;
    nodes.value[0].data.item = response.value.data.item;
    nodes.value[0].data.name = response.value.data.usuario_criacao;
    nodes.value[1].data.name = response.value.data.usuario_comunicador;
    nodes.value[1].data.data = response.value.data.data_comunicador;
    nodes.value[1].data.item = response.value.data.item;
    nodes.value[2].data.name = response.value.data.usuario_aprovacao_gerencial;
    nodes.value[2].data.data = response.value.data.data_aprovacao_gerencial;
    nodes.value[2].data.item = response.value.data.item;

    if (response.value.data.Status === "Criado") {
      nodes.value[0].style.borderColor = "green";
      updateNodeIcon("0", true);
    } else if (
      response.value.data.Status === "Pendente - Analista comunicador"
    ) {
      nodes.value[0].style.borderColor = "green";
      nodes.value[1].style.borderColor = "green";
    } else if (response.value.data.Status === "Pendente - Validação Gerencia") {
      nodes.value[0].style.borderColor = "green";
      nodes.value[1].style.borderColor = "green";
      nodes.value[2].style.borderColor = "green";
    } else if (response.value.data.Status === "Finalizado") {
      nodes.value[0].style.borderColor = "green";
      nodes.value[1].style.borderColor = "green";
      nodes.value[2].style.borderColor = "green";
      nodes.value[3].style.borderColor = "green";
    }
  }
});

const nodes = ref([
  {
    id: "0",
    type: "custominput",
    icon: false,
    data: {
      item: "Michael Corleone ajksdhgfjksdahfjkdsahfdjsakfhasdkjfhdsajkfhasdjkfhdsjkfhajkfadshfkjadf",
      name: "João",
      data: "",
      status: "Criado",
    },
    position: { x: 0, y: 150 },
    style: {
      borderColor: "gray",
    },
  },
  {
    id: "A",
    type: "custom",
    icon: false,
    data: {
      name: "Maria",
      status: "Pendente - Analista comunicador",
      data: "14/04/2024",
      item: "Michael Corleone",
    },
    style: {
      borderColor: "gray",
    },
    position: { x: 250, y: 150 },
  },
  {
    id: "B",
    type: "custom",
    icon: false,
    data: {
      name: "José",
      status: "Pendente - Aprovação gerencia",
      item: "Michael Corleone",
      data: "15/04/2024",
    },
    style: {
      borderColor: "gray",
    },
    position: { x: 350, y: 150 },
  },
  {
    id: "C",
    type: "custom",
    icon: false,
    data: {
      name: "Marcelo",
      status: "Finalizado",
      data: "15/04/2024",
      item: "Michael Corleone",
    },
    style: {
      borderColor: "gray",
    },
    position: { x: 450, y: 150 },
  },
]);

const edges = ref([
  { id: "e0-A", source: "0", target: "A" },
  { id: "eA-B", source: "A", target: "B" },
  { id: "eB-C", source: "B", target: "C" },
]);

function onConnectStart({ nodeId, handleType }) {
  return console.log("on connect start", { nodeId, handleType });
}

function onConnectEnd(event) {
  return console.log("on connect end", event);
}

function onConnect(params) {
  console.log("on connect", params);
  addEdges(params);
}
</script>

<template>
  <VueFlow
    style="background-color: #13120d"
    :nodes="nodes"
    :edges="edges"
    fit-view-on-init
    class="validationflow"
    @connect="onConnect"
    @connect-start="onConnectStart"
    @connect-end="onConnectEnd"
  >
    <h1 style="color: white; font-family: 'Reddit Sans', sans-serif">
      Cadastro de cliente
    </h1>
    <template #node-custominput="props">
      <CustomInput v-bind="props" />
    </template>

    <template #node-custom="props">
      <CustomNode
        :id="props.id"
        :name="props.name"
        :status="props.status"
        :data="props.data"
        :item="props.item"
        :icon="props.icon"
      />
    </template>
  </VueFlow>
</template>

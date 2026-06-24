<template>
  <div>
    <h1>Monte seu pedido</h1>
    <pedido-component-vue :sorvete="this.sorveteSelecionado" />
  </div>
</template>
<script>
import PedidoComponentVue from "@/components/PedidoComponent.vue";
export default {
  name: "ConfiguracaoPedidoView",
  components: {
    PedidoComponentVue,
  },
  data() {
    return {
      sorveteSelecionado: null,
    };
  },
  mounted() {
    const query = this.$route.query;
    if (query.sorvete) {
      const decodeSorvete = JSON.parse(decodeURIComponent(query.sorvete));
      this.sorveteSelecionado = decodeSorvete;
    }
  },
};
</script>
<style scoped></style>

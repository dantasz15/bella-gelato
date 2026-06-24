<template>
  <div>
    <AlertaComponent
      :tipo="alerta.tipo"
      :mensagem="alerta.mensagem"
      :visivel="alerta.visivel"
    />
    <div id="pedidos-tabela">
      <div>
        <div id="pedidos-tabela-cabecalho">
          <div id="ordem-id">#ID</div>
          <div>Nome</div>
          <div>Sabor</div>
          <div>Servir</div>
          <div>Tamanho</div>
          <div>Opcionais</div>
          <div>Status</div>
          <div id="div-acoes">Ações</div>
        </div>
      </div>
    </div>
    <div
      class="pedidos-tabela-linha"
      v-for="pedido in listaPedidosRealizados"
      :key="pedido.id"
    >
      <div id="ordem-numero">{{ pedido.id }}</div>
      <div>{{ pedido.nome }}</div>
      <div>{{ pedido.sorvete.nome }}</div>
      <div>{{ pedido.servico.descricao }}</div>
      <div>{{ pedido.tamanho.descricao }}</div>
      <div>
        <ul>
          <li v-for="(cobertura, index) in pedido.coberturas" :key="index">
            {{ cobertura.nome }}
          </li>
        </ul>
        <div class="divider"></div>
        <ul>
          <li v-for="(adicional, index) in pedido.adicionais" :key="index">
            {{ adicional.nome }}
          </li>
        </ul>
      </div>
      <div>
        <select
          name="status"
          class="status"
          @change="atualizarStatusPedido($event, pedido.id)"
        >
          <option value="">Selecione</option>
          <option
            v-for="status in listaStatusPedido"
            :key="status.id"
            :value="status.id"
            :selected="status.id == pedido.statusId"
          >
            {{ status.descricao }}
          </option>
        </select>
      </div>
      <div id="div-acoes">
        <img
          @click="deletarPedido(pedido.id)"
          src="/img/lixeira.svg"
          width="35px"
          height="35px"
        />
      </div>
    </div>
  </div>
</template>
<script>
import AlertaComponent from "@/components/AlertaComponent.vue";

export default {
  name: "ListaPedidoComponent",
  components: {
    AlertaComponent,
  },
  data() {
    return {
      listaPedidosRealizados: [],
      listaStatusPedido: [],
      alerta: {
        visivel: false,
        tipo: "info",
        mensagem: "",
      },
    };
  },
  methods: {
    mostrarAlerta(tipo, mensagem) {
      this.alerta = { visivel: true, tipo, mensagem };
    },
    async consultarPedidos() {
      const response = await fetch(`${this.$apiUrl}/pedidos`);
      const dados = await response.json();
      this.listaPedidosRealizados = dados;
    },
    async consultarStatusPedido() {
      const response = await fetch(`${this.$apiUrl}/status_pedido`);
      this.listaStatusPedido = await response.json();
    },
    async deletarPedido(id) {
      try {
        const response = await fetch(`${this.$apiUrl}/pedidos/${id}`, {
          method: "DELETE",
        });

        if (!response.ok) {
          throw new Error("Falha ao excluir o pedido");
        }

        // Atualização em tempo real: remove o pedido da lista local,
        // fazendo a interface re-renderizar imediatamente sem novo fetch.
        this.listaPedidosRealizados = this.listaPedidosRealizados.filter(
          (pedido) => pedido.id !== id
        );
        this.mostrarAlerta("sucesso", "Pedido excluído com sucesso!");
      } catch (erro) {
        this.mostrarAlerta(
          "erro",
          "Não foi possível excluir o pedido. Tente novamente."
        );
      }
    },
    async atualizarStatusPedido(event, idPedido) {
      const idStatusAtualizado = event.target.value;
      const atualizacaoJson = JSON.stringify({ statusId: idStatusAtualizado });
      try {
        const response = await fetch(`${this.$apiUrl}/pedidos/${idPedido}`, {
          method: "PATCH",
          headers: { "Content-Type": "application/json" },
          body: atualizacaoJson,
        });

        if (!response.ok) {
          throw new Error("Falha ao atualizar o status");
        }
        this.mostrarAlerta("info", "Status do pedido atualizado.");
      } catch (erro) {
        this.mostrarAlerta("erro", "Não foi possível atualizar o status.");
      }
    },
  },
  mounted() {
    this.consultarPedidos();
    this.consultarStatusPedido();
  },
};
</script>
<style scoped>
#pedidos-tabela {
  width: 100%;
  margin: 0 auto;
}

#pedidos-tabela-cabecalho,
#pedidos-tabela-linhas,
.pedidos-tabela-linha {
  display: flex;
  flex-wrap: wrap;
}

#pedidos-tabela-cabecalho {
  font-weight: bold;
  padding: 12px;
  border-bottom: 2px solid #ad1457;
}

#pedidos-tabela-cabecalho div,
.pedidos-tabela-linha div {
  width: 15%;
}

.pedidos-tabela-linha {
  width: 100%;
  padding: 12px;
  border-bottom: 1px dotted #ad1457;
}

#pedidos-tabela-cabecalho #ordem-id,
.pedidos-tabela-linha #ordem-numero,
.pedidos-tabela-linha #div-acoes,
#pedidos-tabela-cabecalho #div-acoes {
  width: 5%;
}

.status {
  padding: 8px;
  border-radius: 8px;
  border: solid #ad1457 1px;
}

#div-acoes img {
  cursor: pointer;
}
</style>

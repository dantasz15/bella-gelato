<template>
  <div>
    <AlertaComponent
      :tipo="alerta.tipo"
      :mensagem="alerta.mensagem"
      :visivel="alerta.visivel"
    />
    <form id="pedido-form" @submit="criarPedido($event)">
      <div id="sorvete-cabecalho">
        <p id="nome-sorvete-content">
          {{ sorvete && sorvete.nome ? sorvete.nome : "-" }}
        </p>
        <img id="foto-content" :src="sorvete && sorvete.foto ? sorvete.foto : ''" />
      </div>
      <div class="inputs">
        <label for="nome-cliente">Nome</label>
        <input
          type="text"
          v-model="nomeCliente"
          id="nome-cliente"
          name="nome-cliente"
          placeholder="Digite seu Nome"
        />
      </div>
      <div class="inputs">
        <label>Como servir</label>
        <select name="servico" id="servico" v-model="servicoSelecionado">
          <option value="" selected>Selecione</option>
          <option
            v-for="servico in listaTiposServico"
            :key="servico.id"
            :value="servico"
          >
            {{ servico.descricao }}
          </option>
        </select>
      </div>
      <div class="inputs">
        <label>Tamanho</label>
        <select name="tamanho" id="tamanho" v-model="tamanhoSelecionado">
          <option value="" selected>Selecione o tamanho</option>
          <option
            v-for="tamanho in listaTamanhos"
            :key="tamanho.id"
            :value="tamanho"
          >
            {{ tamanho.descricao }}
          </option>
        </select>
      </div>
      <div class="inputs">
        <label id="opcionais-titulo"> Selecione os opcionais</label>
        <label id="opcionais-subtitulo"> Escolha as coberturas</label>

        <div
          class="checkbox-container"
          v-for="cobertura in listaCoberturas"
          :key="cobertura.id"
        >
          <input
            type="checkbox"
            :name="cobertura.nome"
            :value="cobertura"
            v-model="listaCoberturasSelecionadas"
          />
          <span>{{ cobertura.nome }}</span>
        </div>

        <label> Adicionais</label>

        <div
          class="checkbox-container"
          id="checkbox-container"
          v-for="adicional in listaAdicionais"
          :key="adicional.id"
        >
          <input
            type="checkbox"
            :name="adicional.nome"
            :value="adicional"
            v-model="listaAdicionaisSelecionados"
          />
          <span>{{ adicional.nome }}</span>
        </div>
        <div class="inputs">
          <input type="submit" class="submit-btn" value="Confirmar Pedido" />
        </div>
      </div>
    </form>
  </div>
</template>
<script>
import AlertaComponent from "@/components/AlertaComponent.vue";

export default {
  name: "PedidoComponent",
  components: {
    AlertaComponent,
  },
  props: {
    sorvete: null,
  },
  data() {
    return {
      listaTiposServico: [],
      listaTamanhos: [],
      listaCoberturas: [],
      listaAdicionais: [],
      nomeCliente: "",
      servicoSelecionado: "",
      tamanhoSelecionado: "",
      listaCoberturasSelecionadas: [],
      listaAdicionaisSelecionados: [],
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
    async getTiposServico() {
      const response = await fetch(`${this.$apiUrl}/tipos_servico`);
      const dados = await response.json();
      this.listaTiposServico = dados;
    },
    async getTamanhos() {
      const response = await fetch(`${this.$apiUrl}/tamanhos`);
      const dados = await response.json();
      this.listaTamanhos = dados;
    },
    async getOpcionais() {
      const response = await fetch(`${this.$apiUrl}/opcionais`);
      const dados = await response.json();
      this.listaCoberturas = dados.coberturas;
      this.listaAdicionais = dados.adicionais;
    },
    validarPedido() {
      if (!this.sorvete || !this.sorvete.nome) {
        this.mostrarAlerta(
          "aviso",
          "Volte ao Menu e selecione um sabor antes de continuar."
        );
        return false;
      }
      if (this.nomeCliente.trim() === "") {
        this.mostrarAlerta("erro", "Informe o nome do cliente para continuar.");
        return false;
      }
      if (this.servicoSelecionado === "") {
        this.mostrarAlerta("erro", "Selecione como deseja ser servido.");
        return false;
      }
      if (this.tamanhoSelecionado === "") {
        this.mostrarAlerta("erro", "Selecione o tamanho do seu sorvete.");
        return false;
      }
      return true;
    },
    async criarPedido(e) {
      e.preventDefault();

      if (!this.validarPedido()) {
        return;
      }

      const dadosPedido = {
        nome: this.nomeCliente,
        servico: this.servicoSelecionado,
        tamanho: this.tamanhoSelecionado,
        coberturas: Array.from(this.listaCoberturasSelecionadas),
        adicionais: Array.from(this.listaAdicionaisSelecionados),
        sorvete: this.sorvete,
        statusId: 1,
      };

      const dadosJson = JSON.stringify(dadosPedido);

      try {
        const req = await fetch(`${this.$apiUrl}/pedidos`, {
          method: "POST",
          headers: { "Content-Type": "application/json" },
          body: dadosJson,
        });

        if (!req.ok) {
          throw new Error("Falha ao registrar o pedido");
        }

        this.mostrarAlerta("sucesso", "Pedido confirmado com sucesso!");
        setTimeout(() => {
          this.$router.push("/pedidos");
        }, 1200);
      } catch (erro) {
        this.mostrarAlerta(
          "erro",
          "Não foi possível registrar o pedido. Tente novamente."
        );
      }
    },
  },
  mounted() {
    this.getTiposServico();
    this.getTamanhos();
    this.getOpcionais();
  },
};
</script>
<style scoped>
#pedido-form {
  max-width: 750px;
  margin: 0 auto;
  text-align: left;
  padding: 0 16px;
}

#sorvete-cabecalho {
  margin-bottom: 16px;
}

#nome-sorvete-content {
  margin: 0 0 12px 0;
  font-size: 36px;
  font-weight: bold;
  text-align: left;
  color: #ad1457;
}

#foto-content {
  display: block;
  width: 100%;
  height: 220px;
  object-fit: cover;
  border-radius: 16px;
}

.inputs {
  display: flex;
  flex-direction: column;
  margin-bottom: 16px;
}

label {
  font-weight: bold;
  margin-bottom: 12px;
  color: #ad1457;
  padding: 5px 12px;
  display: flex;
  border-left: 4px solid #f48fb1;
}

input,
select {
  padding: 12px;
  width: 100%;
  max-width: 320px;
  border: solid #ad1457 1px;
  border-radius: 8px;
  height: 20px;
  font-size: 12px;
  box-sizing: content-box;
}

select {
  height: 45px;
}

#opcionais-titulo {
  width: 100%;
}

#opcionais-subtitulo {
  width: 100%;
  margin-bottom: 12px;
}

.checkbox-container {
  margin-bottom: 8px;
}

.checkbox-container span {
  margin-left: 6px;
  font-weight: bold;
}

.checkbox-container span,
.checkbox-container input {
  width: auto;
  height: 20px;
}

.submit-btn {
  background-color: #ad1457;
  color: #ffe0b2;
  font-weight: bold;
  border: none;
  font-size: 18px;
  border-radius: 12px;
  padding: 16px;
  margin-top: 12px;
  cursor: pointer;
  width: 100%;
  height: auto;
  transition: 0.5s;
}

.submit-btn:hover {
  background-color: #f48fb1;
  color: #ad1457;
}
</style>

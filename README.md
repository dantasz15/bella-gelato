# 🍦 Bella Gelato

Sistema de pedidos de uma **sorveteria**, desenvolvido em **Vue 3**, evoluído e
customizado a partir do sistema base **T-Burguer** (trabalhado em sala de aula).
A estrutura original foi integralmente reaproveitada — Options API, `vue-router`,
`fetch`, propriedade global `$apiUrl` e JSON Server — e adaptada para um novo
segmento comercial.

---

## 1. Visão Geral

O segmento escolhido foi uma **sorveteria**. O cliente navega por um cardápio de
sabores, monta seu pedido (escolhendo **como servir**, **tamanho**, **coberturas** e
**adicionais**) e acompanha a lista de pedidos realizados, podendo alterar o status e
excluir registros.

### Alterações estruturais (dados e regras)

O campo *"Ponto da carne"* do T-Burguer não faz sentido para uma sorveteria e foi
substituído por *"Como servir"* (casquinha, cascão, copinho, pote). Foi criado o campo
obrigatório *"Tamanho"* (1, 2 ou 3 bolas). O objeto principal `burguer` virou
`sorvete`, e os opcionais foram remodelados:

| T-Burguer (original) | Bella Gelato (novo) |
|---|---|
| `burguer` (item principal) | `sorvete` |
| `tipos_pontos` / *"Ponto da carne"* | `tipos_servico` / *"Como servir"* |
| *(não existia)* | `tamanhos` / *"Tamanho"* (campo novo) |
| `opcionais.complemento` | `opcionais.coberturas` |
| `opcionais.bebidas` | `opcionais.adicionais` |
| `menu.burgues` | `menu.sorvetes` |

Reflexo dessas mudanças no objeto enviado para a API ao confirmar um pedido:

```js
// PedidoComponent.vue
const dadosPedido = {
  nome: this.nomeCliente,
  servico: this.servicoSelecionado,   // antes: ponto (ponto da carne)
  tamanho: this.tamanhoSelecionado,   // campo novo
  coberturas: Array.from(this.listaCoberturasSelecionadas),
  adicionais: Array.from(this.listaAdicionaisSelecionados),
  sorvete: this.sorvete,              // antes: burguer
  statusId: 1,
};
```

### Alterações visuais

Novo nome, logo, banner, troca completa das imagens (sorvetes), textos e paleta de
cores (de dourado/preto para rosa/framboesa). Exemplo na barra de navegação:

```css
/* NavBarComponent.vue */
#nav {
  background-color: #ad1457;        /* framboesa */
  border-bottom: #f48fb1 4px solid; /* rosa */
}
```

---

## 2. Solução Técnica dos Alertas

A comunicação visual foi centralizada em um único componente reutilizável,
`AlertaComponent.vue`, que recebe três `props`: `tipo`, `mensagem` e `visivel`.
O `tipo` define a cor semântica e o ícone exibido:

| Tipo | Cor | Uso |
|---|---|---|
| `erro` | 🔴 Vermelho | Erros de preenchimento ou ações inválidas |
| `aviso` | 🟠 Laranja | Avisos importantes |
| `info` | 🔵 Azul | Informações contextuais |
| `sucesso` | 🟢 Verde | Sucesso ao cadastrar, editar ou excluir |

A **exibição dinâmica** funciona assim: a cor é aplicada por *class binding*
(`alerta-${tipo}`) e o ícone é retornado por um método (`obterIcone`) a partir do
`tipo`:

```js
// AlertaComponent.vue
methods: {
  obterIcone() {
    const icones = { erro: "✕", aviso: "⚠", info: "ℹ", sucesso: "✓" };
    return icones[this.tipo] || "ℹ";
  },
},
```

```html
<div v-if="visivel" :class="['alerta', `alerta-${tipo}`]" role="alert">
  <span class="alerta-icone">{{ obterIcone() }}</span>
  <span class="alerta-mensagem">{{ mensagem }}</span>
</div>
```

Cada tela mantém um objeto reativo `alerta` em `data()` e um método único
`mostrarAlerta(tipo, mensagem)` que dispara o alerta certo:

```js
data() {
  return { alerta: { visivel: false, tipo: "info", mensagem: "" } };
},
methods: {
  mostrarAlerta(tipo, mensagem) {
    this.alerta = { visivel: true, tipo, mensagem };
  },
}
```

A **lógica de validação** roda antes de enviar o pedido: `validarPedido()` bloqueia a
confirmação quando falta um campo obrigatório e dispara o alerta vermelho de erro:

```js
validarPedido() {
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
}
```

Em caso de sucesso, é exibido o alerta verde e o usuário é redirecionado
automaticamente para a tela de pedidos. Na exclusão, o registro é removido do array
local (re-renderização imediata) seguido do alerta verde de sucesso.

---

## 3. Link da API

URL pública da API mockada (JSON Server, publicada no Render):

**‹INSIRA AQUI A URL DA SUA API›**

---

## 4. Link de Produção

Link ativo do projeto publicado (Vercel):

**‹INSIRA AQUI O LINK .vercel.app›**

---

## 5. Link do Repositório

Código-fonte público no GitHub:

- **Front-end:** ‹INSIRA AQUI O LINK DO REPOSITÓRIO DO FRONT›
- **Banco-json (API):** ‹INSIRA AQUI O LINK DO REPOSITÓRIO DO BANCO›

---

## ⚙️ Como rodar localmente

```bash
npm install          # instala as dependências
npm run bancojson    # inicia o JSON Server na porta 3000
npm run serve        # em outro terminal, roda a aplicação
```

> Crie um `.env.development` (copie de `.env.exemplo`) com
> `VUE_APP_API_BASE_URL=http://localhost:3000`. O Vue só lê o `.env` ao iniciar;
> após editar, reinicie o `npm run serve`.

# 🍦 Banco Bella Gelato API

API mockada desenvolvida com JSON Server para fornecer os dados do sistema **Bella Gelato**, uma evolução do projeto T-Burguer desenvolvida com Vue 3.

Esta API é responsável pelo armazenamento e gerenciamento dos dados consumidos pela aplicação, incluindo cardápio, pedidos e status dos pedidos.

---

# 📖 Visão Geral

O projeto **Bella Gelato** foi criado a partir da adaptação completa do sistema T-Burguer para o segmento de sorveteria.

A API fornece os dados necessários para:

* Exibição do cardápio de sorvetes
* Consulta dos tipos de serviço
* Consulta dos tamanhos disponíveis
* Consulta de coberturas e adicionais
* Cadastro de pedidos
* Atualização do status dos pedidos
* Exclusão de pedidos
* Monitoramento da produção dos pedidos

---

# 🗂 Estrutura dos Dados

## Menu

```json
{
  "sorvetes": [],
  "tipos_servico": [],
  "tamanhos": [],
  "coberturas": [],
  "adicionais": []
}
```

## Pedido

```json
{
  "id": 1,
  "nome": "Gabriel",
  "servico": "Casquinha",
  "tamanho": "2 bolas",
  "coberturas": ["Chocolate"],
  "adicionais": ["Granulado"],
  "sorvete": "Morango",
  "statusId": 1
}
```

## Status do Pedido

```json
{
  "id": 1,
  "tipo": "Solicitado"
}
```

---

# 🚀 Endpoints Disponíveis

## Cardápio

```http
GET /menu
```

Retorna todos os dados necessários para montagem dos pedidos.

---

## Pedidos

```http
GET /pedidos
POST /pedidos
PATCH /pedidos/:id
DELETE /pedidos/:id
```

Permite consultar, cadastrar, atualizar e remover pedidos.

---

## Status dos Pedidos

```http
GET /status_pedido
```

Retorna os status utilizados para acompanhamento dos pedidos.

---

# 🌐 API Publicada

A API está disponível publicamente através do Render:

**URL Base**

https://banco-bella-gelato1.onrender.com

### Exemplos de Endpoints

**Menu**

https://banco-bella-gelato1.onrender.com/menu

**Pedidos**

https://banco-bella-gelato1.onrender.com/pedidos

**Status dos Pedidos**

https://banco-bella-gelato1.onrender.com/status_pedido

---

# 🛠 Tecnologias Utilizadas

* Node.js
* JSON Server
* Render
* Git
* GitHub

---

# ⚙️ Como Executar Localmente

## Instalar dependências

```bash
npm install
```

## Iniciar a API

```bash
npm start
```

A API ficará disponível em:

```http
http://localhost:3000
```

---

# 📂 Repositórios do Projeto

## Front-end (Vue 3)

https://github.com/dantasz15/bella-gelato

## API JSON Server

https://github.com/dantasz15/banco-bella-gelato

---

# 🌍 Sistema em Produção

Aplicação publicada no Vercel:

https://bella-gelato.vercel.app

---

# 🔗 Arquitetura da Solução

```text
Usuário
   │
   ▼
Bella Gelato (Vue 3)
https://bella-gelato.vercel.app
   │
   ▼
JSON Server API
https://banco-bella-gelato1.onrender.com
   │
   ▼
db.json
```

---

# 👨‍💻 Autor

**Gabriel Dantas**

Projeto acadêmico desenvolvido para a disciplina de Desenvolvimento Web com Vue 3, aplicando conceitos de:

* Componentização
* Vue Router
* Consumo de APIs REST
* JSON Server
* Experiência do Usuário (UX)
* Validação de Formulários
* Deploy em Produção (Vercel e Render)
* Versionamento com Git e GitHub

---

# 📄 Licença

Projeto desenvolvido para fins acadêmicos e educacionais.

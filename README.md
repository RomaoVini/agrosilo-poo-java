# 🌾 AgroSilo — Sistema de Gestão de Estoque de Grãos

O **AgroSilo** é uma aplicação desenvolvida em **Java 11+**, executada via terminal (CLI), destinada à gestão de estoques de soja em armazéns de cooperativas agrícolas.

O sistema permite cadastrar contas de armazenagem de produtores, registrar movimentações de estoque e realizar a apuração de resultados financeiros utilizando cotações diárias e diferentes métodos de valoração de estoque.

---

## 🚀 Funcionalidades

### 👨‍🌾 Gestão de Contas de Armazenagem

* Cadastro de contas vinculadas aos produtores e armazéns;
* Consulta de contas;
* Edição de dados;
* Exclusão de contas;
* Consulta do saldo e histórico de movimentações.

### 📦 Movimentações de Estoque

O sistema permite registrar:

* **Entradas** de grãos;
* **Saídas** de grãos;
* **Quebras técnicas**, limitadas a **1% do saldo disponível**;
* Atualização do estoque de forma consistente e atômica;
* Histórico cronológico das movimentações.

### 💰 Valoração do Estoque

O AgroSilo suporta dois métodos de valoração:

* **Custo Médio Ponderado Móvel**;
* **PEPS (Primeiro que Entra, Primeiro que Sai)**.

O método de valoração pode ser selecionado durante a execução do sistema sem alterar o histórico das movimentações já registradas.

### 📈 Cotações da Soja

O sistema possui um **Oráculo de Cotações** responsável pela obtenção da cotação diária da saca de soja.

Recursos disponíveis:

* Consulta de cotações diárias;
* Cache de até **365 entradas**;
* Busca retroativa automática de até **5 dias** quando não houver cotação disponível para a data solicitada;
* Utilização de **Decorator/Proxy** para gerenciamento do cache.

### 💾 Persistência

O sistema possui duas opções de persistência:

* **Memória:** utilizada para testes e demonstrações;
* **MariaDB:** utilizada para persistência em banco de dados.

A implementação pode ser escolhida durante a inicialização do sistema.

### 🌎 Internacionalização

A interface do sistema oferece suporte aos seguintes idiomas:

* 🇧🇷 Português (Brasil);
* 🇺🇸 Inglês;
* 🇪🇸 Espanhol.

Textos, mensagens e formatações são adaptados de acordo com o idioma selecionado.

### 📊 Relatórios

O sistema disponibiliza informações como:

* Saldo atual de estoque;
* Histórico de movimentações;
* Resultados realizados;
* Resultados não realizados;
* Informações consolidadas por conta;
* Valor de mercado do estoque;
* Custos associados ao estoque.

### 🖥️ Interface CLI

A aplicação possui uma interface executada diretamente pelo terminal, com:

* Menus interativos;
* Formatação de informações;
* Cores ANSI;
* Opção de desativação das cores quando necessário;
* Suporte a múltiplos idiomas.

---

## 🏗️ Arquitetura

O projeto utiliza a arquitetura **Model-View-Controller (MVC)** adaptada para uma aplicação de terminal.

```text
┌─────────────┐
│    View     │
│   (CLI)     │
└──────┬──────┘
       │
       ▼
┌─────────────┐
│ Controller  │
└──────┬──────┘
       │
       ▼
┌─────────────┐
│    Model    │
│             │
│ Entidades   │
│ Regras      │
│ Contratos   │
└──────┬──────┘
       │
       ▼
┌─────────────────────┐
│ Persistência /      │
│ Oráculo de Cotação  │
└─────────────────────┘
```

### Model

Responsável por:

* Entidades do domínio;
* Regras de negócio;
* Interfaces e contratos;
* Métodos de valoração;
* Comunicação com as camadas de persistência e serviços.

### View

Responsável por:

* Exibição das informações;
* Menus;
* Entrada de dados;
* Mensagens ao usuário;
* Formatação;
* Cores ANSI;
* Internacionalização da interface.

A View **não deve conter regras de negócio**.

### Controller

Responsável por:

* Receber as ações da View;
* Validar entradas;
* Acionar as operações necessárias;
* Coordenar a comunicação entre View e Model.

---

## 🧩 Padrões de Projeto

O projeto utiliza diferentes padrões de projeto para manter a aplicação desacoplada e extensível.

### DAO — Data Access Object

Isola o acesso aos dados das regras de negócio.

Permite utilizar diferentes implementações de persistência, como:

```text
DAO
├── Memória
└── MariaDB
```

Dessa forma, a camada de negócio não precisa conhecer os detalhes de armazenamento.

### DTO — Data Transfer Object

Utilizado para transportar dados consolidados entre as camadas, principalmente em relatórios, evitando a exposição direta das entidades do domínio.

### Factory

Responsável por criar as implementações concretas utilizadas pelo sistema de acordo com a configuração escolhida durante a inicialização.

### Strategy

Utilizado para permitir a troca do algoritmo de valoração do estoque:

```text
Strategy
├── Custo Médio Ponderado Móvel
└── PEPS
```

Novos métodos de valoração podem ser adicionados sem modificar diretamente as demais regras do sistema.

### Decorator / Proxy

Utilizado para adicionar o mecanismo de cache ao serviço responsável pelas cotações.

O cache reduz a necessidade de realizar consultas repetidas ao serviço de cotação.

---

## 🛠️ Requisitos

Para executar o projeto, é necessário possuir:

* **JDK 11 ou superior**;
* **MariaDB 10.x ou superior**, caso seja utilizado o modo com banco de dados;
* **Driver JDBC do MariaDB**.

### Verificando o Java

```bash
java -version
javac -version
```

---

## 💻 Compilação

### Linux / macOS

Para compilar o projeto utilizando o JDK:

```bash
mkdir -p bin
javac -Xlint:all -d bin $(find src -name "*.java")
```

### Windows PowerShell

```powershell
New-Item -ItemType Directory -Force bin
javac -Xlint:all -d bin (Get-ChildItem -Recurse -Filter *.java src | ForEach-Object { $_.FullName })
```

---

## ▶️ Execução

Após a compilação, execute a classe principal do projeto:

```bash
java -cp bin <ClassePrincipal>
```

> Substitua `<ClassePrincipal>` pelo nome completo da classe responsável por iniciar a aplicação.

Caso o projeto utilize um pacote, informe o caminho completo da classe:

```bash
java -cp bin pacote.subpacote.ClassePrincipal
```

---

## 🗄️ MariaDB

Para utilizar a persistência em MariaDB, é necessário:

1. Instalar o MariaDB;
2. Criar o banco de dados;
3. Configurar as credenciais de acesso;
4. Disponibilizar o driver JDBC;
5. Iniciar a aplicação utilizando a implementação de persistência do MariaDB.

A configuração específica de conexão deve ser definida conforme a estrutura do projeto.

---

## 📁 Estrutura do Projeto

Uma organização esperada para o projeto é:

```text
AgroSilo/
├── src/
│   ├── model/
│   ├── view/
│   ├── controller/
│   ├── dao/
│   ├── dto/
│   ├── factory/
│   ├── strategy/
│   └── ...
│
├── bin/
│
├── README.md
└── ...
```

A estrutura pode variar conforme a implementação final do projet

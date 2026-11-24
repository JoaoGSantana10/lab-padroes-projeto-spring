# Lab - Padrões de Projeto com Spring

![Java](https://img.shields.io/badge/Java-25-ED8B00?style=for-the-badge&logo=openjdk&logoColor=white)
![Spring Boot](https://img.shields.io/badge/Spring_Boot-4.1.1-6DB33F?style=for-the-badge&logo=springboot&logoColor=white)
![Maven](https://img.shields.io/badge/Maven-C71A36?style=for-the-badge&logo=apachemaven&logoColor=white)
![H2](https://img.shields.io/badge/H2-Database-1E88E5?style=for-the-badge&logo=h2&logoColor=white)
![OpenFeign](https://img.shields.io/badge/OpenFeign-Declarative_Client-8BC34A?style=for-the-badge)
![OpenAPI](https://img.shields.io/badge/OpenAPI-3.1.1-6BA539?style=for-the-badge&logo=openapiinitiative&logoColor=white)

Uma API REST didática que demonstra como o Spring Framework aplica padrões de projeto em uma aplicação real. O sistema gerencia clientes e utiliza o ViaCEP para obter e persistir seus dados de endereço.

> Este projeto foi criado para estudar, na prática, **Singleton, Strategy, Facade, Repository, Injeção de Dependências e integração HTTP declarativa**.

---

## Visão geral

```text
Cliente HTTP
     |
     v
ClienteRestController  <---- Facade REST
     |
     v
ClienteService          <---- Strategy
     |
     v
ClienteServiceImpl      <---- Singleton gerenciado pelo Spring
     |           |
     |           v
     |       ViaCepService <---- OpenFeign
     |           |
     v           v
ClienteRepository  EnderecoRepository
     |                 |
     +-------- Banco H2
```

## Tecnologias

| Tecnologia | Papel no projeto |
|---|---|
| Java 25 | Linguagem principal |
| Spring Boot 4.1.1 | Inicialização e configuração da aplicação |
| Spring Web MVC | Exposição da API REST |
| Spring Data JPA | Mapeamento objeto-relacional e repositórios |
| H2 Database | Banco de dados em memória para estudos |
| OpenFeign | Cliente HTTP declarativo para o ViaCEP |
| Springdoc OpenAPI | Documentação interativa da API |
| Maven | Gerenciamento de dependências e build |

## Funcionalidades

- Listar todos os clientes
- Buscar um cliente pelo identificador
- Inserir um cliente e resolver seu endereço pelo CEP
- Atualizar um cliente existente
- Excluir um cliente
- Consultar dados de CEP pela API pública ViaCEP
- Reutilizar um endereço já armazenado no banco quando o CEP já existir

## Estrutura do projeto

```text
src/main/java/one/digitalinnovation/gof
├── Application.java
├── controller
│   └── ClienteRestController.java
├── model
│   ├── Cliente.java
│   ├── ClienteRepository.java
│   ├── Endereco.java
│   └── EnderecoRepository.java
└── service
    ├── ClienteService.java
    ├── ViaCepService.java
    └── impl
        └── ClienteServiceImpl.java
```

## Como executar

Pré-requisitos:

- JDK 25
- Maven ou o Maven Wrapper incluído no projeto

Na raiz do projeto:

```bash
./mvnw spring-boot:run
```

No Windows:

```powershell
mvnw.cmd spring-boot:run
```

A aplicação começa em `http://localhost:8080` por padrão.

## Endpoints

| Método | Caminho | Descrição |
|---|---|---|
| `GET` | `/clientes` | Lista todos os clientes |
| `GET` | `/clientes/{id}` | Busca um cliente pelo ID |
| `POST` | `/clientes` | Insere um cliente |
| `PUT` | `/clientes/{id}` | Atualiza um cliente |
| `DELETE` | `/clientes/{id}` | Remove um cliente |

Exemplo de corpo para inserir um cliente:

```json
{
  "nome": "João Guilherme",
  "endereco": {
    "cep": "01001000"
  }
}
```

## Interfaces úteis

- Swagger UI: `http://localhost:8080/swagger-ui/index.html`
- OpenAPI JSON: `http://localhost:8080/v3/api-docs`
- Console H2: `http://localhost:8080/h2-console`

## Testes

```bash
./mvnw test
```

## Estudos detalhados

A documentação foi organizada para funcionar como um guia de leitura do projeto:

- [📚 Índice da documentação](docs/README.md)
- [🏗️ Arquitetura e fluxo das requisições](docs/arquitetura.md)
- [🧩 Explicação de todas as classes](docs/classes.md)
- [🎯 Padrões de projeto estudados](docs/padroes.md)
- [🔌 API REST e exemplos](docs/api.md)
- [🧠 Minha leitura do projeto](docs/aprendizado.md)

## Observações do estado atual

O projeto está funcional como laboratório, mas ainda possui pontos de evolução naturais:

- `buscarPorId` e alguns fluxos devem tratar melhor o recurso inexistente
- `atualizar` deve preservar ou alterar os dados do cliente pela entidade encontrada
- a validação de CEP e os casos de erro podem ser fortalecidos
- os testes atuais só verificam o carregamento do contexto Spring

Esses itens aparecem como oportunidades de aprendizado na documentação e não foram alterados no funcionamento original sem uma evolução prática proposta.


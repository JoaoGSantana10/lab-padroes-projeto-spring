# Padrões de projeto estudados

## Singleton gerenciado pelo Spring

Por padrão, um bean criado pelo Spring possui escopo Singleton.

`ClienteServiceImpl` está anotado com `@Service`, portanto o Spring cria uma única instância para o contexto da aplicação e a reutiliza nos locais que pedem `ClienteService`.

### O padrão aqui

```text
uma instância compartilhada
  -> controller
  -> aplicação
```

### Observação

O padrão não é implementado manualmente com variável estática e construtor privado. O Spring implementa o gerenciamento do ciclo de vida dos beans.

### Inversão de controle

Com `@Autowired`, o objeto não cria seus próprios repositórios. O Spring cria ou localiza os componentes e injeta suas dependências.

---

## Strategy

`ClienteService` é a interface que declara o contrato. `ClienteServiceImpl` é uma estratégia concreta.

### Estrutura

```text
ClienteRestController
         |
         v
ClienteService  <--- contrato
         |
         v
ClienteServiceImpl
```

### Por que usar

O controller conhece o contrato e não precisa saber qual implementação o atende. Uma nova implementação poderia ser criada para uma regra diferente, armazenamento alternativo ou testes.

### Aprendizado

Esse é um bom exemplo de programação orientada a interfaces: dependemos de abstrações, não de detalhes internos.

---

## Facade

O controller atua como uma fachada simples para operações relacionadas ao cliente.

Sem o controller, cada consumidor teria que conhecer:

- como acessar os repositórios;
- como consultar o ViaCEP;
- como verificar se o endereço já existe;
- como salvar o cliente e seu endereço.

Com a fachada REST, o consumidor precisa conhecer apenas endpoints HTTP como `GET /clientes` e `POST /clientes`.

### Diagrama

```text
Complexidade interna
  banco H2
  repositórios JPA
  ViaCEP
  regras de endereço
          |
          v
  ClienteRestController
          |
          v
  API REST simples
```

### Limitação

Em projetos maiores, o controller apenas encaminha ao service. `ClienteServiceImpl` também concentra vários detalhes de integração; isso se aproxima de uma Facade de aplicação, mas ainda pode ser dividido em componentes menores.

---

## Repository

`ClienteRepository` e `EnderecoRepository` representam o padrão Repository.

Eles isolam o serviço da complexidade de persistência e oferecem operações orientadas às entidades.

### Benefícios

- menos SQL ou JPA espalhado pela aplicação;
- testes mais fáceis por abstração;
- troca de mecanismo de persistência menos acoplada ao domínio.

---

## Proxy / Client HTTP declarativo

ViaCEP é representado por `ViaCepService`.

Na prática, OpenFeign cria um proxy da interface em tempo de execução. Ao chamar `consultarCep`, o proxy monta a requisição, envia o HTTP e converte a resposta em `Endereco`.

O restante da aplicação trabalha com uma interface Java, como se estivesse chamando um método local.

---

## Resumo dos padrões no projeto

| Padrão | Onde aparece | Problema que ajuda a resolver |
|---|---|---|
| Singleton | Beans do Spring | Reutilização controlada de componentes |
| Strategy | `ClienteService` / `ClienteServiceImpl` | Troca de implementação sem mudar o consumidor |
| Facade | Controller REST e fluxo de aplicação | Interface simples sobre subsistemas |
| Repository | Repositórios JPA | Separação da persistência |
| Dependency Injection | `@Autowired` | Criação e conexão automática de objetos |
| Proxy | `ViaCepService` | Encapsulamento de chamadas HTTP |

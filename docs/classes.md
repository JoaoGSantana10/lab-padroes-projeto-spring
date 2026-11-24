# Explicação de todas as classes

## Application

**Arquivo:** `src/main/java/one/digitalinnovation/gof/Application.java`

É o ponto de partida da aplicação.

```java
@EnableFeignClients
@SpringBootApplication
public class Application
```

### O que cada anotação faz

- `@SpringBootApplication` configura a aplicação Spring Boot e ativa a busca de componentes no pacote;
- `@EnableFeignClients` ativa a criação dos clientes HTTP declarativos;
- `main` chama `SpringApplication.run(...)`.

### Como o Spring a utiliza

Ao iniciar o projeto, o Spring encontra o pacote base, lê as anotações e cria os beans necessários. Essa classe não contém regra de negócio.

---

## ClienteRestController

**Arquivo:** `src/main/java/one/digitalinnovation/gof/controller/ClienteRestController.java`

É a porta de entrada da API REST.

### Anotações principais

- `@RestController`: combina comportamento de controller e resposta HTTP em JSON;
- `@RequestMapping("clientes")`: define o prefixo `/clientes`;
- `@GetMapping`, `@PostMapping`, `@PutMapping`, `@DeleteMapping`: definem operações HTTP;
- `@PathVariable`: conecta `{id}` da URL a um parâmetro Java;
- `@RequestBody`: converte o JSON recebido em `Cliente`.

### Métodos

| Método | HTTP | O que faz |
|---|---|---|
| `buscarTodos` | GET `/clientes` | Solicita todos os clientes |
| `buscarPorId` | GET `/clientes/{id}` | Solicita um cliente pelo ID |
| `inserir` | POST `/clientes` | Encaminha um novo cliente |
| `atualizar` | PUT `/clientes/{id}` | Encaminha os dados para atualização |
| `deletar` | DELETE `/clientes/{id}` | Solicita a exclusão pelo ID |

### Responsabilidade

O controller não acessa diretamente os repositórios. Ele recebe uma requisição e delega a execução ao `ClienteService`.

### Ponto de atenção

As operações retornam `ResponseEntity.ok(...)`, incluindo erros de recurso inexistente. Em uma API mais rigorosa, métodos como `buscarPorId` deveriam retornar `404 Not Found` quando o cliente não existisse.

---

## Cliente

**Arquivo:** `src/main/java/one/digitalinnovation/gof/model/Cliente.java`

Representa a entidade Cliente armazenada no banco.

### Atributos

| Atributo | Tipo | Papel |
|---|---|---|
| `id` | `Long` | Identificador gerado automaticamente |
| `nome` | `String` | Nome do cliente |
| `endereco` | `Endereco` | Endereço associado ao cliente |

### Anotações JPA

- `@Entity`: transforma a classe em entidade persistente;
- `@Id`: identifica a chave primária;
- `@GeneratedValue(strategy = GenerationType.AUTO)`: delega a geração do ID ao provedor JPA;
- `@ManyToOne`: indica que muitos clientes podem compartilhar um endereço.

### Getters e setters

Os métodos de acesso permitem que JPA, Spring MVC, Jackson e outros componentes leiam ou alterem os atributos.

---

## Endereco

**Arquivo:** `src/main/java/one/digitalinnovation/gof/model/Endereco.java`

Representa os dados retornados pelo ViaCEP e armazenados no banco.

### Atributos

| Atributo | Significado |
|---|---|
| `cep` | Código de Endereçamento Postal, usado como chave primária |
| `logradouro` | Rua, avenida ou via |
| `complemento` | Informação complementar |
| `unidade` | Unidade vinculada ao endereço |
| `bairro` | Bairro |
| `localidade` | Cidade |
| `uf` | Unidade federativa abreviada |
| `estado` | Nome completo do estado |
| `regiao` | Região do Brasil |
| `ibge` | Código municipal do IBGE |
| `gia` | Código GIA |
| `ddd` | DDD |
| `siafi` | Código SIAFI |

### Como surgiu

A classe foi gerada a partir do JSON da API ViaCEP. Essa estrutura permite que o OpenFeign desserialize diretamente a resposta HTTP em um objeto Java.

### Ponto de atenção

Alguns campos são opcionais ou podem não aparecer em determinada resposta da ViaCEP. Em evoluções futuras, um tipo opcional ou validação personalizada pode representar melhor esses casos.

---

## ClienteRepository

**Arquivo:** `src/main/java/one/digitalinnovation/gof/model/ClienteRepository.java`

Extende `CrudRepository<Cliente, Long>`.

### Generics

- `Cliente`: entidade administrada;
- `Long`: tipo da chave primária.

### Operações fornecidas

Sem declarar SQL manualmente, o Spring fornece métodos como:

- `findAll`;
- `findById`;
- `save`;
- `deleteById`.

---

## EnderecoRepository

**Arquivo:** `src/main/java/one/digitalinnovation/gof/model/EnderecoRepository.java`

Extende `CrudRepository<Endereco, String>`.

Nesta interface:

- `Endereco` é a entidade;
- `String` é o tipo da chave primária, pois o `cep` é o ID.

É por ele que o serviço verifica se um CEP já foi salvo.

---

## ClienteService

**Arquivo:** `src/main/java/one/digitalinnovation/gof/service/ClienteService.java`

Declara o contrato dos casos de uso relacionados ao cliente.

### Métodos

- `buscarTodos`
- `buscarPorId`
- `inserir`
- `atualizar`
- `deletar`

A interface separa **o que** o sistema precisa fazer de **como** está implementado. Isso permite trocar ou adicionar implementações sem forçar o controller a conhecer uma implementação específica.

---

## ClienteServiceImpl

**Arquivo:** `src/main/java/one/digitalinnovation/gof/service/impl/ClienteServiceImpl.java`

É a implementação concreta da interface `ClienteService`.

### Dependências

- `ClienteRepository`: persiste clientes;
- `EnderecoRepository`: persiste e localiza endereços;
- `ViaCepService`: consulta endereços externos.

### Método privado

`salvarClienteComCep` concentra a lógica de integração:

```text
recebe cliente
  -> obtém o CEP
  -> procura o endereço no banco
  -> consulta ViaCEP se necessário
  -> salva o novo endereço
  -> associa o endereço ao cliente
  -> salva o cliente
```

### Comportamento atual

- `buscarTodos` chama `findAll`;
- `buscarPorId` usa `findById` e chama `get`;
- `inserir` reutiliza `salvarClienteComCep`;
- `atualizar` verifica se o cliente existe e chama o mesmo método de salvamento;
- `deletar` usa `deleteById`.

### Pontos de atenção

`Optional.get()` pode lançar uma exceção quando o ID não existe. Uma implementação madura deve testar `isPresent` ou retornar uma exceção de domínio/http adequada.

Além disso, atualizar deveria aplicar os dados recebidos ao objeto já encontrado, em vez de apenas executar o mesmo caminho de salvamento.

---

## ViaCepService

**Arquivo:** `src/main/java/one/digitalinnovation/gof/service/ViaCepService.java`

É um cliente HTTP baseado em OpenFeign.

```java
@FeignClient(name = "viacep", url = "https://viacep.com.br/ws")
```

- `name`: nome lógico do cliente;
- `url`: endereço base da API.

O método `consultarCep` usa `GET /{cep}/json/` e informa ao Feign que a resposta JSON deve ser convertida em `Endereco`.

O projeto não precisa montar manualmente URL, abrir conexão ou converter JSON; o OpenFeign e o Spring fazem a integração declarativa.

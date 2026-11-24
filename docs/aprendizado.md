# Minha leitura do projeto

## Primeira impressão

Este projeto é pequeno no tamanho do código, mas reúne conceitos importantes de uma aplicação Spring organizada. A principal lição não está em apenas fazer uma requisição funcionar; está em separar responsabilidades.

## O que pensei ao estudar o código

### 1. O controller não deveria conhecer o banco

`ClienteRestController` depende de `ClienteService`, não de `ClienteRepository`.

Isso é uma boa separação: mudanças em persistência não precisam alcançar diretamente o endpoint HTTP.

### 2. A interface protege o controller

`ClienteService` funciona como uma barreira entre o controller e os detalhes da implementação.

Se a implementação passar a usar outro banco ou outra regra, o controller continuará chamando os mesmos métodos.

### 3. A lógica principal ficou na implementação do service

`ClienteServiceImpl` coordena:

- consulta de cliente;
- consulta de endereço;
- integração externa;
- persistência;
- vínculo entre entidades.

Para o tamanho atual do projeto, isso é didático. Em um projeto real maior, operações externas e casos de erro poderiam ser isolados com mais serviços ou componentes.

### 4. Endereço é uma entidade reutilizável

Usar CEP como chave faz sentido para evitar que o mesmo endereço seja consultado ou registrado várias vezes.

Porém, a estratégia precisa considerar que os dados de um endereço podem mudar com o tempo. Não basta dizer “CEP já existe, portanto nunca consultar ViaCEP”; em um sistema real pode ser necessário cache, atualização ou controle de validade.

### 5. Os padrões aparecem por composição

Os padrões não são usados isoladamente:

- Spring cria o Singleton;
- Strategy permite trocar implementação;
- Facade simplifica a API;
- Repository separa persistência;
- OpenFeign cria o proxy da API externa.

## Fluxo completo explicado em uma frase

O controller recebe HTTP, o service decide como resolver o CEP, os repositórios persistem os dados, o Feign consulta o ViaCEP e o Spring conecta tudo.

## O que eu mudaria em uma próxima etapa

### Primeiro: preservar o cliente encontrado

No método `atualizar`, seria melhor buscar `clienteBd` e aplicar os campos recebidos antes de salvar.

Pergunta para estudar:

> Como o sistema sabe quais campos o usuário realmente deseja alterar?

### Segundo: tratar cliente não encontrado

`buscarPorId` não deve chamar `Optional.get()` sem confirmar que existe.

Pergunta para estudar:

> Qual resposta HTTP representa melhor um ID que não existe: `404`, `204` ou `400`?

### Terceiro: validar o CEP

Um CEP inválido não deveria chegar à integração externa.

Pergunta para estudar:

> Onde validar: no controller, no service ou em um objeto de domínio?

### Quarto: converter erros externos

Quando ViaCEP estiver indisponível ou o CEP não existir, a API não deveria entregar uma falha técnica diretamente ao cliente.

Pergunta para estudar:

> Como representar uma falha externa em uma resposta HTTP consistente?

## O que este projeto ensina

- configuração inicial do Spring;
- criação de API REST;
- persistência JPA;
- uso de banco em memória;
- integração HTTP declarativa;
- injeção de dependências;
- divisão por responsabilidades;
- aplicação prática de padrões de projeto;
- documentação orientada ao aprendizado.

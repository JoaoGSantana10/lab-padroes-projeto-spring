# Documentação do projeto

Esta documentação explica o projeto em sequência didática. Ela começa pelo ponto de entrada da aplicação e avança pelas classes, pelas integrações e pelos padrões utilizados.

## Ordem sugerida de estudo

1. [Arquitetura e fluxo das requisições](arquitetura.md)
2. [Explicação de todas as classes](classes.md)
3. [Padrões de projeto estudados](padroes.md)
4. [API REST e exemplos](api.md)
5. [Minha leitura do projeto](aprendizado.md)
## Pergunta norteadora

> Como uma requisição HTTP pode chegar ao cliente e usar o ViaCEP, o banco de dados e regras de aplicação sem acoplar tudo dentro do controller?

A resposta aparece em camadas:

- o **controller** recebe a requisição HTTP;
- o **service** concentra o caso de uso;
- os **repositories** persistem os dados;
- o **Feign client** integra a aplicação ao ViaCEP;
- o **Spring** cria e conecta os componentes.

## Convenções usadas nesta documentação

- **Classe** identifica o arquivo Java estudado.
- **Responsabilidade** explica o motivo da classe existir.
- **Composição** indica quais componentes ela usa.
- **Fluxo** descreve o caminho de uma operação.
- **Ponto de atenção** mostra uma oportunidade de melhoria ou um detalhe que merece estudo.

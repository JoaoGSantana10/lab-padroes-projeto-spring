# API REST e exemplos

## Base URL

```text
http://localhost:8080
```

## Modelo de cliente

```json
{
  "id": 1,
  "nome": "João Guilherme",
  "endereco": {
    "cep": "01001000",
    "logradouro": "Praça da Sé",
    "complemento": "lado ímpar",
    "unidade": "",
    "bairro": "Sé",
    "localidade": "São Paulo",
    "uf": "SP",
    "estado": "São Paulo",
    "regiao": "Sudeste",
    "ibge": "3550308",
    "gia": "1004",
    "ddd": "11",
    "siafi": "7107"
  }
}
```

## Listar clientes

```http
GET /clientes
```

Resposta esperada:

```json
[
  {
    "id": 1,
    "nome": "João Guilherme",
    "endereco": {
      "cep": "01001000"
    }
  }
]
```

Exemplo com cURL:

```bash
curl -X GET http://localhost:8080/clientes
```

## Buscar cliente por ID

```http
GET /clientes/{id}
```

Exemplo:

```bash
curl -X GET http://localhost:8080/clientes/1
```

## Inserir cliente

```http
POST /clientes
Content-Type: application/json
```

Corpo mínimo:

```json
{
  "nome": "João Guilherme",
  "endereco": {
    "cep": "01001000"
  }
}
```

Exemplo:

```bash
curl -X POST http://localhost:8080/clientes \
  -H "Content-Type: application/json" \
  -d '{
    "nome": "João Guilherme",
    "endereco": {
      "cep": "01001000"
    }
  }'
```

## Atualizar cliente

```http
PUT /clientes/{id}
Content-Type: application/json
```

Exemplo:

```bash
curl -X PUT http://localhost:8080/clientes/1 \
  -H "Content-Type: application/json" \
  -d '{
    "nome": "João Atualizado",
    "endereco": {
      "cep": "01001000"
    }
  }'
```

## Excluir cliente

```http
DELETE /clientes/{id}
```

Exemplo:

```bash
curl -X DELETE http://localhost:8080/clientes/1
```

## Swagger/OpenAPI

Com a aplicação iniciada:

- Swagger UI: <http://localhost:8080/swagger-ui/index.html>
- especificação: <http://localhost:8080/v3/api-docs>

## Console do H2

- Console: <http://localhost:8080/h2-console>
- JDBC URL: definida pelo Spring Boot em execução

O banco é em memória, portanto os dados são perdidos quando a aplicação reinicia.

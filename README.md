
# 🚀 AquaRescue - API REST com Java + Oracle usando Docker

Este projeto containeriza a aplicação **AquaRescue**, desenvolvida com Spring Boot e conectada a um banco Oracle. A solução é composta por dois containers Docker: um para a aplicação e outro para o banco de dados Oracle XE, seguindo boas práticas de infraestrutura como código.

AquaRescue é uma solução inovadora desenvolvida para mitigar os impactos dos eventos climáticos extremos nas comunidades mais vulneráveis, por meio do monitoramento, previsão e gestão eficiente de dados hidrometeorológicos. Essa aplicação conta com uma API RESTful desenvolvida em Java com Spring Boot, integrando persistência em banco Oracle, autenticação com JWT e documentação via Swagger.

---

## 📦 Estrutura da Solução

- ✅ Container da aplicação Java (com `Dockerfile`)
- ✅ Container Oracle XE (imagem pública)
- ✅ Integração entre aplicação e banco de dados
- ✅ Volume para persistência de dados
- ✅ Uso de variáveis de ambiente
- ✅ Acesso via navegador ao Swagger UI

---

## Links Úteis

- 🎥 [Projeto Cloud](https://youtu.be/Qz0IRzeb0dc)
- ♨️ [Repositório de Java](https://github.com/andrealtobelli/GS-Java)

## Link extra

- 🎥 [Pitch AquaRescue](https://youtu.be/72TR3quH2Dg)
  
---

## ✉️ Objetivo do Projeto

O objetivo do AquaRescue é fornecer uma interface centralizada para coleta, cálculo e previsão de dados meteorológicos, permitindo que ONGs e comunidades acompanhem condições climáticas e tomem decisões com base em dados.

---

## 📁 Requisitos Atendidos

| Requisito                                              | Status |
|--------------------------------------------------------|--------|
| Dockerfile com build multistage                        | ✅     |
| Execução com usuário não-root                          | ✅     |
| Diretório de trabalho definido                         | ✅     |
| Variável de ambiente na aplicação                      | ✅     |
| Porta 8080 exposta                                     | ✅     |
| CRUD completo com persistência no Oracle               | ✅     |
| Volume nomeado no banco                                | ✅     |
| Porta 1521 exposta do Oracle                           | ✅     |
| Containers em segundo plano                            | ✅     |
| Exibição de logs no terminal                           | ✅     |

---

## 🛠️ Como Subir os Containers

### 1. Clonar o projeto

```bash
https://github.com/andrealtobelli/GS-Java.git
```

---

### 2. Subir o container Oracle XE

```bash
docker run -d \
  --name oracle-db \
  -p 1521:1521 \
  -p 5500:5500 \
  -e ORACLE_PWD=admin123 \
  -e ORACLE_CHARACTERSET=AL32UTF8 \
  -v oracle-data:/opt/oracle/oradata \
  container-registry.oracle.com/database/express:21.3.0-xe
```

---

### 3. Criar imagem e rodar o container da aplicação
Construir imagem personalizada
```bash
cd aquarescue
docker build -t aquarescue-api .
```
Rodar o container Java com link para Oracle
```bash
docker run -d \
  --name aquarescue-api \
  -p 8080:8080 \
  --link oracle-db \
  aquarescue-api
```

---

## 🌐 Acessar a Aplicação

Acesse via navegador:

```
http://<IP_DA_VM>:8080/swagger-ui/index.html
```

---

## 📊 Exibir Logs dos Containers

```bash
docker logs -f oracle-db
docker logs -f aquarescue-api
```

---

## 🛡️ Detalhes do Dockerfile

Este arquivo Dockerfile está disponível nos arquivos do repositório.
```dockerfile
# Etapa 1: Build da aplicação
FROM maven:3.8.5-openjdk-17 AS build
WORKDIR /app
COPY . .
RUN mvn clean package -DskipTests

# Etapa 2: Imagem final para rodar a aplicação
FROM openjdk:17-jdk-slim

# Variável de ambiente obrigatória
ENV APP_ENV=production

# Criar usuário não-root
RUN useradd -ms /bin/bash leleuser
USER leleuser

# Definir diretório de trabalho
WORKDIR /home/leleuser/app

# Copiar o jar do estágio anterior
COPY --from=build /app/target/*.jar app.jar

# Expor porta padrão do Spring Boot
EXPOSE 8080

# Comando para iniciar a aplicação
ENTRYPOINT ["java", "-jar", "app.jar"]
```

---

## 💾 Configuração do Banco Oracle

- JDBC: `jdbc:oracle:thin:@oracle-db:1521/XEPDB1`
 
| Campo               | Valor                       |
| ------------------- | --------------------------- |
| **Tipo de conexão** | **Service name** (não SID!) |
| **Nome do serviço** | `XEPDB1`                    |
| **Usuário**         | `system`                    |
| **Senha**           | `admin123`                  |
| **Host**            | `<IP_DA_SUA_VM>`            |
| **Porta**           | `1521`                      |

---

## 📂 Volumes e Persistência

O banco Oracle utiliza volume nomeado `oracle-data` para persistência de dados:

```bash
-v oracle-data:/opt/oracle/oradata
```

---

## 📸 Evidências via Terminal

```bash
# Ver containers rodando
docker ps

# Logs da aplicação
docker logs -f aquarescue-api

# Logs do banco Oracle
docker logs -f oracle-db
```

# CRUD GS 
Aqui estão os POSTs e PUTs para as demais operações só é necessário o ID gerado após a criação.(arquivo com as operações no repositório)


## Usuários
POST

```json
{
  "nome": "Enrico",
  "email": "enrico@gmail.com",
  "senha": "123456",
  "tipo": "ONG"
}
```
PUT

```json
{
  "nome": "Enrico",
  "email": "enricodelguerra@gmail.com",
  "senha": "654321",
  "tipo": "ONG"
}
```

---

## Comunidade

POST 

```json
{
  "nome": "A COMUNIDADE",
  "latitude": 0.14000,
  "longitude": 0.1234234,
  "qtHabitantes": 2000
}
```
PUT

```json
{
  "nome": "COMUNIDADE x",
  "latitude": 0.14000,
  "longitude": 0.1234234,
  "qtHabitantes": 5000
}
```
---

## Previsões

POST
```json
{
  "comunidadeId": 1,
  "temperatura": 25.0,
  "umidade": 80.0,
  "volumePrevisto": 100.0,
  "dataPrevisao": "2025-06-02T04:26:46"
}
```

PUT

```json
{
  "comunidadeId": 1,
  "temperatura": 22.0,
  "umidade": 100.0,
  "volumePrevisto": 80.0,
  "dataPrevisao": "2025-06-02T04:26:46"
}
```
---

## Dados Meteorológicos

POST

```json
{
  "comunidadeId": 1,
  "dataHora": "2025-06-02T16:40:55.129Z",
  "temperatura": 0.1,
  "umidade": 0.1,
  "ptoOrvalho": 0.1,
  "pressao": 0.1,
  "ventoVeloc": 0.1,
  "ventoDirecao": 0.1,
  "ventoRajada": 0.1,
  "radiacao": 0.1,
  "chuva": 0.1
}
```
PUT

```json
{
  "comunidadeId": 1,
  "dataHora": "2025-06-02T16:40:55.129Z",
  "temperatura": 0.1,
  "umidade": 0.5,
  "ptoOrvalho": 0.1,
  "pressao": 0.5,
  "ventoVeloc": 0.7,
  "ventoDirecao": 0.6,
  "ventoRajada": 0.1,
  "radiacao": 0.3,
  "chuva": 0.1
}
```
---

## Log de Cálculo

POST

```json
{
  "comunidadeId": 1,
  "qtHabitantes": 1073741824,
  "consumoPorPessoa": 0.1,
  "totalVolume": 0.1,
  "dataCalculo": "2025-06-02T16:41:28.620Z"
}
```

PUT

```json
{
  "comunidadeId": 1,
  "qtHabitantes": 1073741824,
  "consumoPorPessoa": 0.1,
  "totalVolume": 0.1,
  "dataCalculo": "2025-06-02T16:41:28.620Z"
}
```

---

## ✅ Resultado Final

- API totalmente funcional com documentação Swagger
- Conectada a um banco Oracle via Docker
- Atende 100% dos critérios técnicos exigidos

---

## 👥 Desenvolvedores

- Leticia Cristina Dos Santos Passos RM: 555241
- André Rogério Vieira Pavanela Altobelli Antunes RM: 554764
- Enrico Figueiredo Del Guerra RM: 558604

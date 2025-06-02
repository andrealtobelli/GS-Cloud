
# 🚀 AquaRescue - API REST com Java + Oracle usando Docker

Este projeto containeriza a aplicação **AquaRescue**, desenvolvida com Spring Boot e conectada a um banco Oracle. A solução é composta por dois containers Docker: um para a aplicação e outro para o banco de dados Oracle XE, seguindo boas práticas de infraestrutura como código.

---

## 📦 Estrutura da Solução

- ✅ Container da aplicação Java (com `Dockerfile` personalizado)
- ✅ Container Oracle XE (imagem pública)
- ✅ Integração entre aplicação e banco de dados
- ✅ Volume para persistência de dados
- ✅ Uso de variáveis de ambiente
- ✅ Acesso via navegador ao Swagger UI

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
git clone https://github.com/seu-usuario/aquarescue.git
cd aquarescue
```

---

### 2. Subir o container Oracle XE

```bash
docker run -d   --name oracle-db   -p 1521:1521   -p 5500:5500   -e ORACLE_PWD=admin123   -e ORACLE_CHARACTERSET=AL32UTF8   -v oracle-data:/opt/oracle/oradata   container-registry.oracle.com/database/express:21.3.0-xe
```

---

### 3. Criar imagem e rodar o container da aplicação

```bash
docker build -t aquarescue-api .
docker run -d   --name aquarescue-api   -p 8080:8080   --link oracle-db   aquarescue-api
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

```dockerfile
FROM maven:3.8.5-openjdk-17 AS build
WORKDIR /app
COPY . .
RUN mvn clean package -DskipTests

FROM openjdk:17-jdk-slim
ENV APP_ENV=production
RUN useradd -ms /bin/bash leleuser
USER leleuser
WORKDIR /home/leleuser/app
COPY --from=build /app/target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

---

## 💾 Configuração do Banco Oracle

- Usuário: `system`
- Senha: `admin123`
- JDBC: `jdbc:oracle:thin:@oracle-db:1521/XEPDB1`

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

---

## ✅ Resultado Final

- API totalmente funcional com documentação Swagger
- Conectada a um banco Oracle via Docker
- Atende 100% dos critérios técnicos exigidos

---

## 📬 Contato

Projeto desenvolvido por **Leticia Cristina** para a disciplina **Java Advanced** – FIAP 💙

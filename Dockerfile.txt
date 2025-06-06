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
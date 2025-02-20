# Configurando o SonarQube no Projeto Local

### 🔹 Testado com:
- React/Vite + Typescript
- Next.js + Typescript
- React Native + Typescript


## 1. Instalar Dependências

### 🔹 Requisitos

Antes de começar, certifique-se de ter instalado:

- Docker (para rodar o SonarQube)
- Node.js (versão 16 ou superior)
- Java 17+ (necessário para o SonarScanner)

## 2. Instalar o Java 17+ (Caso ainda não tenha)

O SonarScanner exige o Java 17+. Se não estiver instalado, siga os passos abaixo:

### 🔹 Instalar via Homebrew (MacOS)

```bash
brew install openjdk@17
```

### 🔹 Configurar as Variáveis de Ambiente (MacOS)

Adicione as variáveis de ambiente ao seu ~/.zshrc:

```bash
echo 'export PATH="/opt/homebrew/opt/openjdk@17/bin:$PATH"' >> ~/.zshrc
echo 'export JAVA_HOME="/opt/homebrew/opt/openjdk@17"' >> ~/.zshrc
source ~/.zshrc
```

Verifique se o Java está instalado corretamente:

```bash
java -version
```

Saída esperada:

```bash
openjdk version "17.x.x"
```

⚠ Possível erro: "Unable to locate a Java Runtime"

- Tente reiniciar o terminal e executar novamente java -version;
- Caso persista, reinstale o OpenJDK e repita os passos.

## 3. Instalar o SonarQube com Docker

Para rodar o SonarQube localmente, crie um arquivo `docker-compose.yml` na raiz do projeto:

```yml
version: "3"

services:
  sonarqube:
    image: sonarqube:community
    container_name: sonarqube
    ports:
      - "9000:9000"
    environment:
      - SONAR_JDBC_URL=jdbc:postgresql://db:5432/sonarqube
      - SONAR_JDBC_USERNAME=sonar
      - SONAR_JDBC_PASSWORD=sonar
    depends_on:
      - db

  db:
    image: postgres:14
    container_name: sonarqube_db
    environment:
      - POSTGRES_USER=sonar
      - POSTGRES_PASSWORD=sonar
      - POSTGRES_DB=sonarqube
    volumes:
      - sonarqube_db_data:/var/lib/postgresql/data

volumes:
  sonarqube_db_data:
```

Agora, inicie o SonarQube:

```bash
docker compose up -d
```

Acesse no navegador: `http://localhost:9000`

Login padrão:

- Usuário: `admin`
- Senha: `admin`

⚠ Possível erro: "Porta 9000 já em uso"

- Verifique se há outra instância do SonarQube rodando (docker ps);
- Se necessário, pare a instância anterior com docker stop sonarqube e tente novamente.

## 4. Criar um Novo Projeto no SonarQube

1. Acesse http://localhost:9000.

2. Vá para Projects > Create New Project.

3. Escolha Manually.

4. Defina um Project Key (exemplo: react-app-with-sonar).

5. Gere um Token de Acesso e salve-o.

## 5. Instalar o SonarScanner no Projeto

No terminal, instale o SonarScanner:

```bash
npm install sonar-scanner --save-dev
```

Crie um arquivo `sonar-project.properties` na raiz do projeto:

```bash
sonar.projectKey=react-app-with-sonar
sonar.sources=.
sonar.host.url=http://localhost:9000
sonar.login=SEU_TOKEN_AQUI
sonar.language=ts
sonar.exclusions=**/node_modules/**, **/*.test.tsx, **/*.test.ts
sonar.test.inclusions=**/*.test.tsx, **/*.test.ts
sonar.typescript.lcov.reportPaths=coverage/lcov.info
```

OBS. Substitua `SEU_TOKEN_AQUI` pelo token gerado na configuração do projeto.

# 6. Executar o SonarScanner

Adicione os scripts no `package.json`:

```JS
"scripts": {
  "sonar": "sonar-scanner"
}
```

Pronto! Agora, execute o SonarScanner:

```bash
npm run sonar
```

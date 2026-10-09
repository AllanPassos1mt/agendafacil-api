# AgendaFácil API

API REST para gestão de agendamentos de pequenos negócios, como barbearias, salões e clínicas. O projeto resolve um problema real: controlar horários, evitar conflitos de agenda e organizar serviços e clientes em um só lugar.

![Status](https://img.shields.io/badge/status-em%20desenvolvimento-yellow)
![Java](https://img.shields.io/badge/Java-25-orange)
![Spring Boot](https://img.shields.io/badge/Spring%20Boot-4.1.1-brightgreen)
![MySQL](https://img.shields.io/badge/MySQL-8.4-blue)
![Docker](https://img.shields.io/badge/Docker-Compose-2496ED)

---

## Sumário

- [Sobre o projeto](#sobre-o-projeto)
- [Funcionalidades](#funcionalidades)
- [Regras de negócio](#regras-de-negócio)
- [Tecnologias](#tecnologias)
- [Arquitetura](#arquitetura)
- [Como executar](#como-executar)
- [Configuração](#configuração)
- [Endpoints](#endpoints)
- [Testes](#testes)
- [Roadmap](#roadmap)
- [Decisões técnicas](#decisões-técnicas)
- [Autor](#autor)

---

## Sobre o projeto

Pequenos negócios costumam controlar a agenda por WhatsApp e caderno, o que gera conflitos de horário, esquecimentos e retrabalho. A **AgendaFácil API** centraliza essa gestão em um back-end simples de consumir por qualquer front-end (web ou mobile).

O projeto também é o meu laboratório para praticar boas práticas de back-end com o ecossistema Spring: migrations versionadas, autenticação com JWT, testes automatizados, containerização e pipeline de CI.

## Funcionalidades

| Módulo | Descrição | Status |
|---|---|---|
| Infraestrutura | Spring Boot + MySQL via Docker Compose + Flyway | Concluído |
| Serviços | CRUD de serviços (nome, duração, preço) | Planejado |
| Agendamentos | Criar, listar e cancelar agendamentos | Planejado |
| Autenticação | Login com JWT e perfis ADMIN e CLIENTE | Planejado |
| Documentação | Swagger / OpenAPI | Planejado |
| Qualidade | Testes unitários e de integração, CI | Planejado |
| Deploy | Aplicação publicada online | Planejado |

## Regras de negócio

- Não podem existir dois agendamentos no mesmo horário.
- Só é possível agendar em datas futuras e dentro do horário de funcionamento.
- O cliente visualiza e cancela apenas os próprios agendamentos, respeitando uma antecedência mínima.
- O administrador gerencia os serviços e visualiza todos os agendamentos.

## Tecnologias

- **Java 25** e **Spring Boot 4.1.1**
- **Spring Web** para os endpoints REST
- **Spring Data JPA / Hibernate** para persistência
- **MySQL 8.4** como banco de dados
- **Flyway** para versionamento do banco (migrations)
- **Bean Validation** para validação dos dados de entrada
- **Lombok** para reduzir código repetitivo
- **Docker e Docker Compose** para subir o banco localmente
- **Maven** (com Maven Wrapper) para build e dependências

Planejado: Spring Security + JWT, Springdoc (Swagger), JUnit 5, Mockito e GitHub Actions.

## Arquitetura

O projeto segue uma arquitetura em camadas:

```
Controller  →  Service  →  Repository  →  Banco de dados
 (HTTP)       (regras)    (persistência)      (MySQL)
```

| Camada | Responsabilidade |
|---|---|
| **Controller** | Recebe as requisições HTTP e devolve as respostas |
| **Service** | Concentra as regras de negócio |
| **Repository** | Acessa o banco de dados via Spring Data JPA |
| **Entity** | Representa as tabelas como classes Java |

Estrutura de pastas:

```
agendafacil/
├── src/
│   ├── main/
│   │   ├── java/com/allan/agendafacil/
│   │   └── resources/
│   │       ├── application.yml
│   │       └── db/migration/        # migrations do Flyway
│   └── test/
├── docker-compose.yml
├── pom.xml
└── README.md
```

## Como executar

### Pré-requisitos

- [JDK](https://adoptium.net) (versão 21 ou superior; o projeto foi gerado com Java 25)
- [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- [Git](https://git-scm.com/)

### Passo a passo

**1. Clone o repositório**

```bash
git clone https://github.com/AllanPassos1mt/agendafacil-api.git
cd agendafacil-api
```

**2. Suba o banco de dados** (com o Docker Desktop aberto)

```bash
docker compose up -d
```

Confira se o contêiner `agendafacil-db` está com status `healthy`:

```bash
docker ps
```

**3. Execute a aplicação**

Linux / macOS:

```bash
./mvnw spring-boot:run
```

Windows (PowerShell):

```powershell
.\mvnw.cmd spring-boot:run
```

**4. Acesse**

A API fica disponível em `http://localhost:8080`.

Para encerrar a aplicação, use `Ctrl + C`. Para desligar o banco:

```bash
docker compose stop
```

## Configuração

As configurações ficam em `src/main/resources/application.yml` e podem ser sobrescritas por variáveis de ambiente, o que facilita o deploy sem alterar o código:

| Variável | Descrição | Valor padrão (local) |
|---|---|---|
| `DB_URL` | URL de conexão JDBC | `jdbc:mysql://localhost:3306/agendafacil` |
| `DB_USER` | Usuário do banco | `agenda` |
| `DB_PASSWORD` | Senha do banco | `agenda123` |

> Os valores padrão servem apenas para desenvolvimento local. Em produção, as credenciais devem ser definidas por variáveis de ambiente.

## Endpoints

> Esta seção será atualizada conforme os endpoints forem implementados. Após a configuração do Swagger, a documentação interativa ficará disponível em `/swagger-ui.html`.

Previsão:

| Método | Rota | Descrição | Acesso |
|---|---|---|---|
| POST | `/auth/login` | Autenticação e geração do token JWT | Público |
| GET | `/servicos` | Lista os serviços | Autenticado |
| POST | `/servicos` | Cria um serviço | ADMIN |
| PUT | `/servicos/{id}` | Atualiza um serviço | ADMIN |
| DELETE | `/servicos/{id}` | Remove um serviço | ADMIN |
| POST | `/agendamentos` | Cria um agendamento | CLIENTE |
| GET | `/agendamentos` | Lista agendamentos (os próprios ou todos, conforme o perfil) | Autenticado |
| PATCH | `/agendamentos/{id}/cancelar` | Cancela um agendamento | Dono ou ADMIN |

## Testes

Os testes serão executados com:

```bash
./mvnw test
```

Planejado: testes unitários das regras de negócio (JUnit 5 + Mockito) e testes de integração dos endpoints.

## Roadmap

- [x] Setup do projeto com Spring Boot
- [x] Banco MySQL com Docker Compose
- [x] Flyway configurado
- [ ] Migrations e CRUD de serviços
- [ ] Agendamentos com regras de negócio
- [ ] Autenticação JWT e perfis de acesso
- [ ] Testes unitários e de integração
- [ ] Documentação com Swagger
- [ ] Dockerfile e pipeline de CI com GitHub Actions
- [ ] Deploy online
- [ ] Front-end em React consumindo a API

## Decisões técnicas

- **Flyway no lugar de `ddl-auto: update`:** o banco evolui por migrations versionadas e revisáveis, e o Hibernate apenas valida o schema (`ddl-auto: validate`). Isso evita alterações silenciosas e torna o histórico do banco rastreável.
- **Configuração por variáveis de ambiente:** o mesmo código roda em desenvolvimento e produção, sem credenciais fixas no repositório de produção.
- **Docker Compose para o banco:** qualquer pessoa consegue rodar o projeto sem instalar o MySQL na máquina.
- **Arquitetura em camadas:** separa responsabilidades e facilita os testes das regras de negócio.

## Autor

**Allan Henrique Passos Lima**
Estudante de Ciência da Computação (Universidade Tiradentes), em busca de estágio em desenvolvimento back-end e full-stack.

- GitHub: [AllanPassos1mt](https://github.com/AllanPassos1mt)
- LinkedIn: [allan-passos](https://www.linkedin.com/in/allan-passos)
- E-mail: allanhenriqq24@gmail.com

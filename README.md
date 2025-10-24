# Sistema de Gerenciamento de Vagas e Candidaturas (PL/pgSQL)

[Este projeto consiste em um sistema de Banco de Dados voltado para o gerenciamento eficiente de vagas e candidaturas.O objetivo é automatizar o fluxo de abertura de vagas, cadastro de candidaturas, busca por currículos (e-mail), busca por habilidades (*hard skills*), e monitoramento do status de vagas, centralizando o processo em um único ambiente

Em resumo, seu objetivo é gerenciar candidatos, vagas, inscrições, habilidades e avaliações.

O projeto foi desenvolvido como parte da disciplina de Banco de Dados II no IFPE Campus Jaboatão dos Guararapes.

## 🛠️ Tecnologias Utilizadas

* **SGBD:** PostgreSQL (usado para as *queries* e estruturas PL/pgSQL).
* **Linguagem de Procedimento:** PL/pgSQL.

## 🗺️ Modelo de Dados (Schema)

O banco de dados é composto por 5 tabelas:

Candidatos

Vagas

Inscricoes

Habilidades

CandidatoHabilidades
### Estrutura das Tabelas

```sql
-- Tabela Candidatos
CREATE TABLE IF NOT EXISTS Candidatos (
    candidato_id SERIAL PRIMARY KEY,
    nome VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    data_nascimento DATE NOT NULL,
    curriculo_link VARCHAR(255) NULL
);

-- Tabela Vagas
CREATE TABLE IF NOT EXISTS Vagas (
    vaga_id SERIAL PRIMARY KEY,
    titulo VARCHAR(100) NOT NULL,
    descricao TEXT,
    data_publicacao DATE DEFAULT CURRENT_DATE,
    status VARCHAR(50) DEFAULT 'Aberta'
);

-- Tabela Inscricoes
CREATE TABLE IF NOT EXISTS Inscricoes (
    inscricao_id SERIAL PRIMARY KEY,
    candidato_id INT REFERENCES Candidatos(candidato_id),
    vaga_id INT REFERENCES Vagas(vaga_id),
    data_inscricao TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    status VARCHAR(50) DEFAULT 'Em Analise'
);

-- Tabela Habilidades
CREATE TABLE IF NOT EXISTS Habilidades (
    habilidade_id SERIAL PRIMARY KEY,
    nome_habilidade VARCHAR(100) UNIQUE NOT NULL
);

-- Tabela CandidatoHabilidades (Relação N:N)
CREATE TABLE IF NOT EXISTS CandidatoHabilidades (
    candidato_id INT REFERENCES Candidatos(candidato_id),
    habilidade_id INT REFERENCES Habilidades(habilidade_id),
    nivel VARCHAR(50),
    PRIMARY KEY (candidato_id, habilidade_id)
);

-- Tabela de Histórico (Criada para a Trigger 3)
CREATE TABLE IF NOT EXISTS InscricoesHistorico (
    log_id SERIAL PRIMARY KEY,
    inscricao_id INT,
    candidato_id INT,
    vaga_id INT,
    data_inscricao TIMESTAMP,
    status VARCHAR(50),
    data_delecao TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

### Preenchendo Tabelas

-- Inserir Candidatos
INSERT INTO Candidatos (nome, email, data_nascimento, curriculo_link) VALUES
('Ana Souza', 'ana.souza@email.com', '1995-03-12', 'https://curriculos.com/ana-souza'),
('Bruno Lima', 'bruno.lima@email.com', '1990-07-24', 'https://curriculos.com/bruno-lima'),
('Carla Mendes', 'carla.mendes@email.com', '1998-11-10', 'https://curriculos.com/carla-mendes');

-- Inserir Vagas
INSERT INTO Vagas (titulo, descricao, data_publicacao, status) VALUES
('Desenvolvedor Front-End', 'Desenvolvimento de interfaces React e integração com APIs REST.', '2025-09-01', 'Aberta'),
('Analista de Dados', 'Coleta e análise de dados para suporte a decisões estratégicas.', '2025-08-25', 'Aberta'),
('Engenheiro de Software', 'Projetar e desenvolver aplicações de larga escala.', '2025-09-10', 'Aberta');

-- Inserir Habilidades
INSERT INTO Habilidades (nome_habilidade) VALUES
('JavaScript'),
('React'),
('Python'),
('SQL'),
('UI/UX Design');

-- Inserir Inscrições
INSERT INTO Inscricoes (candidato_id, vaga_id, data_inscricao, status) VALUES
(1, 1, '2025-09-02 10:30:00', 'Em Analise'),
(2, 3, '2025-09-11 14:00:00', 'Aprovado'),
(3, 2, '2025-09-05 09:15:00', 'Reprovado');

-- Associar Habilidades aos Candidatos
INSERT INTO CandidatoHabilidades (candidato_id, habilidade_id, nivel) VALUES
(1, 1, 'Avançado'),
(1, 2, 'Intermediário'),
(2, 3, 'Avançado'),
(2, 4, 'Avançado'),
(3, 5, 'Intermediário'),
(3, 4, 'Intermediário');

------

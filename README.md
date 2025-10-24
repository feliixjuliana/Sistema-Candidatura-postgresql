# Sistema de Gerenciamento de Vagas e Candidaturas (PL/pgSQL)

[cite_start]Este projeto consiste em um sistema de Banco de Dados voltado para o gerenciamento eficiente de vagas e candidaturas[cite: 4]. [cite_start]O objetivo é automatizar o fluxo de abertura de vagas, cadastro de candidaturas, busca por currículos (e-mail), busca por habilidades (*hard skills*), e monitoramento do status de vagas, centralizando o processo em um único ambiente[cite: 5, 6].

[cite_start]Em resumo, seu objetivo é gerenciar candidatos, vagas, inscrições, habilidades e avaliações[cite: 7].

[cite_start]O projeto foi desenvolvido como parte da disciplina de Banco de Dados II no IFPE Campus Jaboatão dos Guararapes[cite: 1, 2].

## 🛠️ Tecnologias Utilizadas

* **SGBD:** PostgreSQL (usado para as *queries* e estruturas PL/pgSQL).
* [cite_start]**Linguagem de Procedimento:** PL/pgSQL[cite: 3].

## 🗺️ Modelo de Dados (Schema)

[cite_start]O banco de dados é composto por 5 tabelas[cite: 8]:
1.  [cite_start]`Candidatos` [cite: 9]
2.  [cite_start]`Vagas` [cite: 10]
3.  [cite_start]`Inscricoes` [cite: 11]
4.  [cite_start]`Habilidades` [cite: 12]
5.  [cite_start]`CandidatoHabilidades` [cite: 13]

### Estrutura das Tabelas

```sql
-- Tabela Candidatos
CREATE TABLE IF NOT EXISTS Candidatos (
    [cite_start]candidato_id SERIAL PRIMARY KEY, [cite: 17]
    [cite_start]nome VARCHAR(100) NOT NULL, [cite: 18]
    email VARCHAR(100) UNIQUE NOT NULL, [cite: 19]
    data_nascimento DATE NOT NULL, [cite: 20]
    curriculo_link VARCHAR(255) NULL [cite: 21]
);

-- Tabela Vagas
CREATE TABLE IF NOT EXISTS Vagas (
    vaga_id SERIAL PRIMARY KEY, [cite: 23]
    titulo VARCHAR(100) NOT NULL, [cite: 24]
    descricao TEXT, [cite: 25]
    data_publicacao DATE DEFAULT CURRENT_DATE, [cite: 26]
    status VARCHAR(50) DEFAULT 'Aberta' [cite: 27]
);

-- Tabela Inscricoes
CREATE TABLE IF NOT EXISTS Inscricoes (
    inscricao_id SERIAL PRIMARY KEY, [cite: 31]
    candidato_id INT REFERENCES Candidatos (candidato_id), [cite: 32]
    vaga_id INT REFERENCES Vagas (vaga_id), [cite: 32]
    data_inscricao TIMESTAMP DEFAULT CURRENT_TIMESTAMP, [cite: 33]
    status VARCHAR(50) DEFAULT 'Em Analise' [cite: 34]
);

-- Tabela Habilidades
CREATE TABLE IF NOT EXISTS Habilidades (
    habilidade_id SERIAL PRIMARY KEY, [cite: 37]
    nome_habilidade VARCHAR(100) UNIQUE NOT NULL [cite: 38]
);

-- Tabela CandidatoHabilidades (Relação N:N)
CREATE TABLE IF NOT EXISTS CandidatoHabilidades (
    candidato_id INT REFERENCES Candidatos(candidato_id), [cite: 41]
    habilidade_id INT REFERENCES Habilidades (habilidade_id), [cite: 41]
    nivel VARCHAR(50), [cite: 41]
    PRIMARY KEY (candidato_id, habilidade_id) [cite: 42]
);

-- Tabela de Histórico (Criada para a Trigger 3)
[cite_start]CREATE TABLE IF NOT EXISTS InscricoesHistorico ( [cite: 276]
    [cite_start]log_id SERIAL PRIMARY KEY, [cite: 278]
    [cite_start]inscricao_id INT, [cite: 279]
    [cite_start]candidato_id INT, [cite: 280]
    [cite_start]vaga_id INT, [cite: 281]
    [cite_start]data_inscricao TIMESTAMP, [cite: 282]
    [cite_start]status VARCHAR(50), [cite: 283]
    data_delecao TIMESTAMP DEFAULT CURRENT_TIMESTAMP [cite: 284]
);

### Estrutura das Tabelas

-- Inserir Candidatos
INSERT INTO Candidatos (nome, email, data_nascimento, curriculo_link) VALUES
('Ana Souza', 'ana.souza@email.com', '1995-03-12', '[https://curriculos.com/ana-souza](https://curriculos.com/ana-souza)'),
('Bruno Lima', 'bruno.lima@email.com', '1990-07-24', '[https://curriculos.com/bruno-lima](https://curriculos.com/bruno-lima)'),
('Carla Mendes', 'carla.mendes@email.com', '1998-11-10', '[https://curriculos.com/carla-mendes](https://curriculos.com/carla-mendes)');

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

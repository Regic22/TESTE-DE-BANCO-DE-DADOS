-- 1. Criar tabelas para armazenar os dados
CREATE TABLE demonstracoes_contabeis (
    id SERIAL PRIMARY KEY,
    ano INT,
    trimestre INT,
    operadora_id VARCHAR(20),
    nome_operadora TEXT,
    despesas_eventos_saude NUMERIC(15,2)
);

CREATE TABLE operadoras_ativas (
    id SERIAL PRIMARY KEY,
    registro_ans VARCHAR(20),
    nome_social TEXT,
    cnpj VARCHAR(20),
    modalidade TEXT,
    uf CHAR(2),
    municipio TEXT
);

-- 2. Importar os arquivos CSV (PostgreSQL)
COPY demonstracoes_contabeis(ano, trimestre, operadora_id, nome_operadora, despesas_eventos_saude)
FROM '/caminho/para/demonstracoes_contabeis.csv' 
DELIMITER ';' CSV HEADER ENCODING 'UTF8';

COPY operadoras_ativas(registro_ans, nome_social, cnpj, modalidade, uf, municipio)
FROM '/caminho/para/operadoras_ativas.csv' 
DELIMITER ';' CSV HEADER ENCODING 'UTF8';

-- Para MySQL (substituir pelo caminho correto no servidor)
LOAD DATA INFILE '/caminho/para/demonstracoes_contabeis.csv' 
INTO TABLE demonstracoes_contabeis 
FIELDS TERMINATED BY ';' 
LINES TERMINATED BY '\n' 
IGNORE 1 ROWS;

LOAD DATA INFILE '/caminho/para/operadoras_ativas.csv' 
INTO TABLE operadoras_ativas 
FIELDS TERMINATED BY ';' 
LINES TERMINATED BY '\n' 
IGNORE 1 ROWS;

-- 3. Query analítica: 10 operadoras com maiores despesas no último trimestre
SELECT operadora_id, nome_operadora, SUM(despesas_eventos_saude) AS total_despesas
FROM demonstracoes_contabeis
WHERE ano = EXTRACT(YEAR FROM CURRENT_DATE) 
  AND trimestre = (SELECT MAX(trimestre) FROM demonstracoes_contabeis WHERE ano = EXTRACT(YEAR FROM CURRENT_DATE))
GROUP BY operadora_id, nome_operadora
ORDER BY total_despesas DESC
LIMIT 10;

-- 4. Query analítica: 10 operadoras com maiores despesas no último ano
SELECT operadora_id, nome_operadora, SUM(despesas_eventos_saude) AS total_despesas
FROM demonstracoes_contabeis
WHERE ano = EXTRACT(YEAR FROM CURRENT_DATE) - 1
GROUP BY operadora_id, nome_operadora
ORDER BY total_despesas DESC
LIMIT 10;

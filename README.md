

-- Tabela para as operadoras
CREATE TABLE operadoras (
    registro_ans INT PRIMARY KEY,
    cnpj VARCHAR(14),
    razao_social VARCHAR(255),
    nome_fantasia VARCHAR(255),
    modalidade VARCHAR(255),
    endereco VARCHAR(255),
    numero VARCHAR(20),
    complemento VARCHAR(255),
    bairro VARCHAR(255),
    municipio VARCHAR(255),
    uf VARCHAR(2),
    cep VARCHAR(8),
    ddd VARCHAR(2),
    telefone VARCHAR(20),
    fax VARCHAR(20),
    email VARCHAR(255),
    representante VARCHAR(255),
    cargo_representante VARCHAR(255),
    data_registro DATE
);

-- Tabela para as demonstrações contábeis
CREATE TABLE demonstracoes_contabeis (
    data_registro DATE,
    reg_ans INT,
    cnpj VARCHAR(14),
    razao_social VARCHAR(255),
    modalidade VARCHAR(255),
    cd_conta_contabil VARCHAR(20),
    descricao VARCHAR(255),
    vl_conta DECIMAL(15, 2)
);
-- Importar operadoras
LOAD DATA INFILE '/caminho/para/operadoras.csv'
INTO TABLE operadoras
CHARACTER SET utf8mb4
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\n'
IGNORE 1 ROWS;

-- Importar demonstrações contábeis
LOAD DATA INFILE '/caminho/para/demonstracoes_contabeis_2022.csv'
INTO TABLE demonstracoes_contabeis
CHARACTER SET utf8mb4
FIELDS TERMINATED BY ';'
ENCLOSED BY '"'
LINES TERMINATED BY '\n'
IGNORE 1 ROWS;
-- 10 maiores despesas no último trimestre
SELECT 
    o.razao_social,
    SUM(dc.vl_conta) AS total_despesas
FROM 
    demonstracoes_contabeis dc
JOIN 
    operadoras o ON dc.reg_ans = o.registro_ans
WHERE 
    dc.cd_conta_contabil = '41111001' 
    AND dc.data_registro >= DATE_SUB(CURDATE(), INTERVAL 3 MONTH)
GROUP BY 
    o.razao_social
ORDER BY 
    total_despesas DESC
LIMIT 10;

-- 10 maiores despesas no último ano
SELECT 
    o.razao_social,
    SUM(dc.vl_conta) AS total_despesas
FROM 
    demonstracoes_contabeis dc
JOIN 
    operadoras o ON dc.reg_ans = o.registro_ans
WHERE 
    dc.cd_conta_contabil = '41111001'
    AND dc.data_registro >= DATE_SUB(CURDATE(), INTERVAL 1 YEAR)
GROUP BY 
    o.razao_social
ORDER BY 
    total_despesas DESC
LIMIT 10;

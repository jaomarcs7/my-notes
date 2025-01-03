Create um table Funcionarios 
```sql
SELECT name, age
FROM users
WHERE age > 25
ORDER BY age DESC;
```

```sql
CREATE TABLE Funcionarios (

    id_funcionario INT,
    nome VARCHAR(255) NOT NULL,
    cpf VARCHAR(11) NOT NULL,
    data_nascimento DATE,
    email VARCHAR(255),
    telefone VARCHAR(20),
    id_departamento INT,
    data_admissao DATE,
    salario_base DECIMAL(10, 2)

);
```

```sql 
SELECT *
FROM empresa_techcorp.funcionarios
```

```sql
-- Selecionar o esquema (banco de dados)
USE empresa_techcorp;

-- Listar todas as tabelas no esquema selecionado

SHOW TABLES;
```

Quanto eu ganho Anual e por Hora 
Usar o Glassdor para verificar o Salary range da Vaga. 
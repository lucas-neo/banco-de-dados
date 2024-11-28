### **Questões Baseadas no Conteúdo dos Slides**

---

### **Perguntas Fechadas (5)**

#### **1.** Qual função de grupo é usada para contar o número de registros em uma tabela? (**PDF: 07-BD-FUNCOES**)  
a) SUM  
b) AVG  
c) COUNT  
d) MIN  

**Resposta:** c) COUNT  

**Explicação:** A função `COUNT` é usada para contar o número de registros em um conjunto de dados, ignorando valores nulos se aplicada a uma coluna específica. Exemplo:
```sql
SELECT COUNT(*) AS total_linhas FROM employees;
```

---

#### **2.** Qual cláusula é necessária para filtrar os dados agrupados por funções de agregação? (**PDF: 07-BD-FUNCOES**)  
a) GROUP BY  
b) HAVING  
c) WHERE  
d) ORDER BY  

**Resposta:** b) HAVING  

**Explicação:** A cláusula `HAVING` é usada para aplicar condições em grupos de dados criados pelo `GROUP BY`. Exemplo:
```sql
SELECT department_id, AVG(salary)
FROM employees
GROUP BY department_id
HAVING AVG(salary) > 5000;
```

---

#### **3.** O que a função analítica `LEAD()` faz em SQL? (**PDF: 11_UNION_FUNCAO_JANELA**)  
a) Retorna o valor da linha anterior.  
b) Retorna o valor da linha seguinte.  
c) Retorna o número de linhas na janela.  
d) Retorna a classificação de uma linha dentro da janela.  

**Resposta:** b) Retorna o valor da linha seguinte.  

**Explicação:** `LEAD()` permite acessar o valor de uma coluna da próxima linha sem alterar a ordem do conjunto de dados. Exemplo:
```sql
SELECT employee_id, salary, LEAD(salary) OVER (ORDER BY salary) AS prox_salario
FROM employees;
```

---

#### **4.** Qual operador é usado para combinar o resultado de duas consultas SQL, removendo duplicados? (**PDF: 11_UNION_FUNCAO_JANELA**)  
a) INTERSECT  
b) UNION  
c) UNION ALL  
d) EXCEPT  

**Resposta:** b) UNION  

**Explicação:** `UNION` combina os resultados de duas consultas e remove duplicados. Exemplo:
```sql
SELECT name FROM employees
UNION
SELECT name FROM contractors;
```

---

#### **5.** Qual função pode ser usada para adicionar meses a uma data no Oracle? (**PDF: 10_datas_tipos_dados**)  
a) ADD_MONTHS  
b) NEXT_DATE  
c) MONTHS_BETWEEN  
d) DATE_ADD  

**Resposta:** a) ADD_MONTHS  

**Explicação:** `ADD_MONTHS()` adiciona ou subtrai um número específico de meses a uma data. Exemplo:
```sql
SELECT ADD_MONTHS(hire_date, 12) AS data_futura
FROM employees;
```

---

### **Perguntas Abertas (10)**

#### **6.** Explique a diferença entre `RANK()` e `DENSE_RANK()` em funções de janela. Dê um exemplo prático de cada uma. (**PDF: 11_UNION_FUNCAO_JANELA**)

**Resposta:**
- `RANK()`: Atribui a mesma classificação para valores iguais, mas pula números na sequência.
- `DENSE_RANK()`: Também atribui a mesma classificação para valores iguais, mas não pula números na sequência.

**Exemplo**:
```sql
SELECT salary,
       RANK() OVER (ORDER BY salary DESC) AS rank,
       DENSE_RANK() OVER (ORDER BY salary DESC) AS dense_rank
FROM employees;
```

| Salary   | RANK | DENSE_RANK |
|----------|------|------------|
| 7000     | 1    | 1          |
| 7000     | 1    | 1          |
| 5000     | 3    | 2          |

---

#### **7.** Descreva como o operador `LIKE` funciona e forneça três exemplos diferentes. (**PDF: 08_SUBCONSULTA**)

**Resposta:**
- O operador `LIKE` realiza buscas baseadas em padrões.
- **`%`**: Corresponde a zero ou mais caracteres.
- **`_`**: Corresponde a exatamente um caractere.

**Exemplos**:
```sql
-- Começa com 'A'
SELECT * FROM employees WHERE name LIKE 'A%';

-- Termina com 'Z'
SELECT * FROM employees WHERE name LIKE '%Z';

-- Contém 'X'
SELECT * FROM employees WHERE name LIKE '%X%';
```

---

#### **8.** Explique como calcular a média salarial por departamento usando `GROUP BY`. Inclua a consulta SQL. (**PDF: 07-BD-FUNCOES**)

**Resposta:**
```sql
SELECT department_id, AVG(salary) AS media_salarial
FROM employees
GROUP BY department_id;
```
- O comando `GROUP BY` agrupa os dados com base no `department_id`, e `AVG(salary)` calcula a média salarial para cada grupo.

---

#### **9.** O que é uma subconsulta correlacionada? Explique com um exemplo. (**PDF: 08_SUBCONSULTA**)

**Resposta:**
- Uma subconsulta correlacionada depende dos valores da consulta principal para ser executada.

**Exemplo**:
```sql
SELECT e1.employee_id, e1.salary
FROM employees e1
WHERE e1.salary > (
    SELECT AVG(e2.salary)
    FROM employees e2
    WHERE e2.department_id = e1.department_id
);
```
- Aqui, a subconsulta usa `e1.department_id` da consulta principal para calcular a média salarial do mesmo departamento.

---

#### **10.** Explique o propósito e a sintaxe de `UNION ALL`. Como ele difere do `UNION`? Inclua um exemplo. (**PDF: 11_UNION_FUNCAO_JANELA**)

**Resposta:**
- **Propósito**: Combina resultados de duas consultas, incluindo duplicados.
- **Diferença**: Ao contrário de `UNION`, `UNION ALL` não remove duplicados.

**Exemplo**:
```sql
SELECT name FROM employees
UNION ALL
SELECT name FROM contractors;
```

---

#### **11.** Como a função `ROW_NUMBER()` é útil em consultas SQL? Explique com um exemplo prático. (**PDF: 11_UNION_FUNCAO_JANELA**)

**Resposta:**
`ROW_NUMBER()` atribui um número único a cada linha na ordem especificada.

**Exemplo**:
```sql
SELECT employee_id, salary,
       ROW_NUMBER() OVER (ORDER BY salary DESC) AS numero_linha
FROM employees;
```
- Útil para paginação ou identificar a ordem.

---

#### **12.** Uma empresa deseja calcular a diferença em anos entre a data de contratação e a data atual. Explique como fazer isso com SQL. (**PDF: 10_datas_tipos_dados**)

**Resposta:**
```sql
SELECT employee_id, 
       TRUNC(MONTHS_BETWEEN(SYSDATE, hire_date) / 12) AS anos_trabalho
FROM employees;
```
- `MONTHS_BETWEEN` calcula a diferença em meses, e dividimos por 12 para obter anos.

---

#### **13.** O que a função `ADD_MONTHS()` faz? Como seria usada para calcular a data 6 meses após a contratação? (**PDF: 10_datas_tipos_dados**)

**Resposta:**
```sql
SELECT hire_date, ADD_MONTHS(hire_date, 6) AS data_seis_meses
FROM employees;
```
- Adiciona 6 meses à data de contratação.

---

#### **14.** Explique como criar uma `VIEW` que mostra funcionários com salários acima da média geral. (**PDF: 07-BD-FUNCOES**)

**Resposta:**
```sql
CREATE VIEW funcionarios_acima_media AS
SELECT employee_id, salary
FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);
```

---

#### **15.** O que é uma função de agregação? Liste três exemplos e explique cada um. (**PDF: 07-BD-FUNCOES**)

**Resposta:**
- **Definição**: Funções que operam em grupos de dados e retornam um único valor.
- **Exemplos**:
  - `SUM`: Soma os valores.
  - `AVG`: Calcula a média.
  - `COUNT`: Conta o número de registros.

---

Se precisar de mais exemplos ou de algo mais detalhado, é só avisar! 😊

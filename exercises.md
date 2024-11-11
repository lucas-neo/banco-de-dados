Para exibir as colunas de todas as tabelas do banco de dados no LiveSQL (Oracle), você pode consultar a visão `ALL_TAB_COLUMNS`, que armazena informações sobre todas as colunas de tabelas acessíveis ao usuário. A consulta a seguir mostrará o nome da tabela e das colunas, juntamente com o tipo de dado de cada coluna:

```sql
SELECT table_name, column_name, data_type, data_length
FROM all_tab_columns
WHERE owner = 'HR'
ORDER BY table_name, column_id;
```

## Funções de Grupo

```sql
SELECT 
AVG(COMMISSION_PCT) as comissao_media, job_id, FIRST_NAME
from hr.EMPLOYEES
group by job_id, FIRST_NAME
having avg(COMMISSION_PCT)>.25
order by comissao_media
```

Essa consulta exibirá todas as colunas de cada tabela pertencente ao usuário `HR`, ordenadas pelo nome da tabela e pela ordem das colunas dentro de cada tabela. Isso te permite ver a estrutura completa de todas as tabelas e suas colunas em uma única consulta.

Essa query calcula a média das comissões (`COMMISSION_PCT`) para cada combinação de cargo (`job_id`) e primeiro nome (`FIRST_NAME`) dos funcionários na tabela `hr.EMPLOYEES`. A query então filtra esses grupos para mostrar apenas aqueles onde a média da comissão é superior a 0,25. Vamos analisar cada parte:

1. **SELECT AVG(COMMISSION_PCT) AS comissao_media, job_id, FIRST_NAME**:
   - Calcula a média de `COMMISSION_PCT` (percentual de comissão) para cada grupo de `job_id` e `FIRST_NAME`.
   - Renomeia o resultado da média da comissão como `comissao_media`.

2. **FROM hr.EMPLOYEES**:
   - Define a tabela `hr.EMPLOYEES` como a fonte dos dados.

3. **GROUP BY job_id, FIRST_NAME**:
   - Agrupa os resultados por `job_id` e `FIRST_NAME`, ou seja, cada cargo e nome de funcionário forma um grupo separado.
   - Para cada grupo, a média de `COMMISSION_PCT` é calculada.

4. **HAVING AVG(COMMISSION_PCT) > 0.25**:
   - Filtra os grupos para incluir apenas aqueles onde a média da comissão é maior que 0,25.
   - `HAVING` é usado para filtrar resultados de agregações, diferentemente de `WHERE`, que é usado para filtros antes da agregação.

5. **ORDER BY comissao_media**:
   - Ordena o resultado pela coluna `comissao_media`, ou seja, pela média das comissões, em ordem crescente.

#### Exemplo de Saída
A consulta retornará algo como:

| comissao_media | job_id      | FIRST_NAME |
|----------------|-------------|------------|
| 0.30           | IT_PROG     | John       |
| 0.40           | SA_REP      | Alice      |

Aqui, cada linha representa um cargo e nome de funcionário onde a média da comissão supera 0,25, ordenados pela média de comissão (`comissao_media`).

#### Exercícios:

1. Qual a soma dos salários dos empregados?
   
```sql
SELECT
SUM(salary) as soma_dos_funcionarios
FROM HR.EMPLOYEES
```
2. Qual é o salário mais baixo?
   
```sql
SELECT
min(salary) as menor_Salario
FROM HR.EMPLOYEES

-----------------------------
SELECT first_name, salary as menor_salario
FROM HR.EMPLOYEES
where salary = (select min(salary) from hr.employees)

```

3. Qual é o salário mais alto?
   
```sql
SELECT first_name, salary as maior_salario
FROM HR.EMPLOYEES
where salary = (select max(salary) from hr.employees)
```

4. Quantos funcionários trabalham na empresa?
   
```sql
SELECT 
    count(employee_id) as qtd_funcionario
FROM HR.EMPLOYEES

```

5. Qual a média dos salários dos empregados por departamento?
   
```sql
SELECT 
avg(salary) as media_salario, DEPARTMENT_ID
FROM HR.EMPLOYEES
GROUP BY DEPARTMENT_ID

**OUTRA FORMA COM OS NOMES**

SELECT a.DEPARTMENT_NAME AS departamento, AVG(b.salary) AS media_salario
FROM hr.departments a
JOIN hr.employees b ON a.department_id = b.department_id
GROUP BY a.DEPARTMENT_NAME;
```

6. Qual a soma dos salários dos empregados por departamento?
   
```sql
SELECT a.DEPARTMENT_NAME AS departamento, SUM(b.salary) AS soma_salario
FROM hr.departments a
JOIN hr.employees b ON a.department_id = b.department_id
GROUP BY a.DEPARTMENT_NAME;
```

7. Qual é o salário mais baixo por departamento?
```sql
select dp.DEPARTMENT_NAME as departamento, func.salary as menor_salario, func.first_name as nome
from hr.departments dp
join hr.employees func on dp.department_id = func.department_id
where func.salary = (
    select min(salary)
    from hr.employees
    where dp.department_id = department_id
)
```

8. Qual é o salário mais alto por departamento?
```sql
select dp.DEPARTMENT_NAME as departamento, func.salary as maior_salario, func.first_name as nome
from hr.departments dp
join hr.employees func on dp.department_id = func.department_id
where func.salary = (
    select max(salary)
    from hr.employees
    where dp.department_id = department_id
)
```

9. Qual a quantidade de funcionários por departamento?
   
```sql
select department_id, count(*) as qtd_funcionario
from hr.employees
group by department_id
```

10. Qual a média dos salários dos empregados por departamento e por Função?

```sql
select avg(salary) as media_salario, job_id
from hr.employees
group by job_id
```

11. Qual a soma dos salários dos empregados por departamento e por Função?

```sql
select department_id, job_id, sum(salary) as soma_salario
from hr.employees
group by department_id, job_id
```

12. Qual é o salário mais baixo por Departamento e por Função?

```sql
select department_id, job_id, min(salary) as soma_salario
from hr.employees
group by department_id, job_id
```

13. Qual é o salário mais alto por Função?

```sql
select job_id as funcao, max(salary) as maior_salario
from hr.employees
group by job_id
```

14. Qual a quantidade de funcionários por departamento e por Função?

```sql
select department_id, job_id, count (employee_id)
from hr.employees
group by department_id, job_id
```

15. Determine quantos empregados têm a função SA_REP.

```sql
select job_id, count (employee_id)
from hr.employees
where job_id = 'SA_REP'
group by job_id
```

16. Determine a diferença entre o salário mais alto e mais baixo

```sql
select 
    max(salary), 
    min(salary), 
	(max(salary)-min(salary)) as diferenca
from hr.employees
```

17. Determine a diferença entre o salário mais alto e mais baixo por departamento

```sql
select 
    max(salary), 
    min(salary), 
	(max(salary)-min(salary)) as diferenca,
    department_id
from hr.employees
group by department_id
```

18. Encontre todos os departamentos que têm mais de três empregados.

```sql
select 
    department_id,
    count (employee_id) as qtd_func
from hr.employees
having count (employee_id) > 3
group by department_id
```

19. Verifique se todos os números de empregado são únicos.

```sql
SELECT 
    COUNT(*) AS qtd_funcionarios,
    COUNT(DISTINCT employee_id) AS qtd_ids_unicos
FROM hr.employees;
```

20. Qual é o maior salário com comissão (considere a comissão como um ganho percentual sobre o salário)?

```sql
select 
	max(salary + (salary *COMMISSION_PCT)) as maior_salario
from hr.employees
```

21. Qual cargo tem maior comissão?

```sql
SELECT job_id, commission_pct AS maior_comissao
FROM hr.employees
WHERE commission_pct = (SELECT MAX(commission_pct) FROM hr.employees);
```

22. Qual cargo tem menor comissão?

```sql
SELECT DISTINCT job_id, commission_pct AS menor_comissao
FROM hr.employees
WHERE commission_pct = (SELECT min(commission_pct) FROM hr.employees);
```

23. Qual é o maior salário com comissão por data de contratação (considere a comissão como um ganho percentual sobre o salário)?

```sql
SELECT 
    HIRE_DATE,
    NVL(MAX(NVL(salary, 0) + (NVL(salary, 0) * NVL(commission_pct, 0))), 0) AS maior_salario
FROM hr.employees
GROUP BY HIRE_DATE
ORDER BY MAIOR_SALARIO
```

24. Qual é o funcionário mais antigo e o respectivo salário?

```sql
SELECT first_name, last_name, salary, hire_date
FROM hr.employees
WHERE hire_date = (SELECT MIN(hire_date) FROM hr.employees);
```

25. Qual é o funcionário mais antigo e o respectivo salário, considerando apenas os que recebem comissão?

```sql
SELECT first_name, last_name, salary, hire_date, commission_pct
FROM hr.employees
WHERE hire_date = (SELECT MIN(hire_date) FROM hr.employees WHERE commission_pct IS NOT NULL);
```

26. Qual data houveram mais contratações? E menos?

```sql
SELECT hire_date, COUNT(employee_id) AS total_contratacoes
FROM hr.employees
GROUP BY hire_date
ORDER BY total_contratacoes DESC;
```
27. Qual a média dos salários da data com mais contratações? E considerando as comissões?
```sql
WITH Contratacao_Max AS (
    SELECT hire_date
    FROM hr.employees
    GROUP BY hire_date
    ORDER BY COUNT(employee_id) DESC
    FETCH FIRST 1 ROWS ONLY
)
SELECT AVG(salary) AS media_salario, 
       AVG(salary + (salary * NVL(commission_pct, 0))) AS media_salario_com_comissao
FROM hr.employees
WHERE hire_date = (SELECT hire_date FROM Contratacao_Max);

```

28. Qual a diferença de salário (com comissão) do primeiro e do último contratado?

```sql
SELECT 
    (SELECT salary + (salary * NVL(commission_pct, 0)) 
     FROM hr.employees 
     WHERE hire_date = (SELECT MIN(hire_date) FROM hr.employees)
     FETCH FIRST 1 ROWS ONLY) AS salario_primeiro,
     
    (SELECT salary + (salary * NVL(commission_pct, 0)) 
     FROM hr.employees 
     WHERE hire_date = (SELECT MAX(hire_date) FROM hr.employees)
     FETCH FIRST 1 ROWS ONLY) AS salario_ultimo,
     
    ((SELECT salary + (salary * NVL(commission_pct, 0)) 
      FROM hr.employees 
      WHERE hire_date = (SELECT MAX(hire_date) FROM hr.employees)
      FETCH FIRST 1 ROWS ONLY) - 
      
     (SELECT salary + (salary * NVL(commission_pct, 0)) 
      FROM hr.employees 
      WHERE hire_date = (SELECT MIN(hire_date) FROM hr.employees)
      FETCH FIRST 1 ROWS ONLY)
    ) AS diferenca_salario
FROM dual;

```

## Sub-consultas


---

1. Mostre os dados dos funcionários cujo salário é acima da média.

```sql
SELECT *
FROM hr.employees
WHERE salary > (SELECT AVG(salary) FROM hr.employees);
```

 Explicação:
- A subconsulta `(SELECT AVG(salary) FROM hr.employees)` calcula o salário médio de todos os funcionários.
- A consulta principal retorna apenas os funcionários cujo salário é superior à média.

---

 2. Quantos funcionários têm salário (com comissão) final acima da média?

```sql
SELECT COUNT(*) AS funcionarios_acima_media
FROM hr.employees
WHERE (salary + NVL(salary * commission_pct, 0)) > 
      (SELECT AVG(salary + NVL(salary * commission_pct, 0)) FROM hr.employees);
```

 Explicação:
- **(salary + NVL(salary * commission_pct, 0))** calcula o salário final com comissão para cada funcionário.
- A subconsulta `(SELECT AVG(salary + NVL(salary * commission_pct, 0)) FROM hr.employees)` calcula a média dos salários com comissão.
- A consulta principal conta os funcionários cujo salário final (incluindo comissão) está acima da média.

---

 3. Qual(is) cargo(s) tem a média salarial acima da média do salário geral da empresa?

```sql
SELECT job_id
FROM hr.employees
GROUP BY job_id
HAVING AVG(salary) > (SELECT AVG(salary) FROM hr.employees);
```

Explicação:
- A subconsulta `(SELECT AVG(salary) FROM hr.employees)` calcula a média salarial geral da empresa.
- A consulta principal agrupa os dados por `job_id` e usa `HAVING` para filtrar os cargos cuja média salarial está acima da média geral.

---

4. Quantos funcionários de cada departamento têm média salarial inferior à média do próprio departamento?

```sql
SELECT department_id, COUNT(*) AS funcionarios_abaixo_media
FROM hr.employees e1
WHERE salary < (SELECT AVG(salary) 
                FROM hr.employees e2 
                WHERE e1.department_id = e2.department_id)
GROUP BY department_id;
```

Explicação:
- **Subconsulta `(SELECT AVG(salary) FROM hr.employees e2 WHERE e1.department_id = e2.department_id)`**: Calcula a média salarial para cada departamento.
- A consulta principal conta os funcionários com salário abaixo da média do próprio departamento, agrupando-os por `department_id`.

---

5. Mostre os dados dos funcionários que tenham o mesmo salário e a mesma comissão da equipe do gestor 149, mas que não têm registro de departamento.

```sql
SELECT *
FROM hr.employees
WHERE (salary, commission_pct) IN (
          SELECT salary, commission_pct
          FROM hr.employees
          WHERE manager_id = 149
      )
      AND department_id IS NULL;
```

Explicação:
- A subconsulta `(SELECT salary, commission_pct FROM hr.employees WHERE manager_id = 149)` seleciona os salários e comissões da equipe do gestor 149.
- A consulta principal filtra para mostrar os funcionários com esses mesmos valores de salário e comissão, mas sem departamento (`department_id IS NULL`).

---

6. Mostre os funcionários que recebem mais do que todos os cargos `PU_CLERK`, classificando do maior para o menor salário.

```sql
SELECT *
FROM hr.employees
WHERE salary > (SELECT MAX(salary)
                FROM hr.employees
                WHERE job_id = 'PU_CLERK')
ORDER BY salary DESC;
```

Explicação:
- A subconsulta `(SELECT MAX(salary) FROM hr.employees WHERE job_id = 'PU_CLERK')` encontra o maior salário entre os cargos `PU_CLERK`.
- A consulta principal exibe os funcionários que recebem mais do que esse valor, ordenados do maior para o menor salário (`ORDER BY salary DESC`).



### Join
Desafio: Utilizando as tabelas hr.employees e hr.departments desenhe o organograma da empresa:

```sql
select c.JOB_TITLE as Title, a.first_name as Gerente, b.first_name as funcionario, n.job_title as cargo from hr.employees a left join hr.employees b on a.employee_id=b.manager_id left join hr.jobs c on a.job_id = c.job_id left join hr.jobs n on b.job_id = n.job_id

----
SELECT 
    d.department_name AS Departamento,
    c.job_title AS Titulo_Gerente,
    a.first_name || ' ' || a.last_name AS Gerente,
    n.job_title AS Titulo_Funcionario,
    b.first_name || ' ' || b.last_name AS Funcionario
FROM hr.employees a
LEFT JOIN hr.employees b ON a.employee_id = b.manager_id
LEFT JOIN hr.jobs c ON a.job_id = c.job_id
LEFT JOIN hr.jobs n ON b.job_id = n.job_id
LEFT JOIN hr.departments d ON a.department_id = d.department_id
ORDER BY Departamento, Gerente, Funcionario;

```

Explicação:
1. **`d.department_name AS Departamento`**: Exibe o nome do departamento do gerente.
2. **`c.job_title AS Titulo_Gerente`**: Mostra o cargo do gerente.
3. **`a.first_name || ' ' || a.last_name AS Gerente`**: Mostra o nome completo do gerente.
4. **`n.job_title AS Titulo_Funcionario`**: Exibe o cargo do funcionário.
5. **`b.first_name || ' ' || b.last_name AS Funcionario`**: Mostra o nome completo do funcionário que reporta ao gerente.
6. **`ORDER BY Departamento, Gerente, Funcionario`**: Ordena os resultados pelo nome do departamento, depois pelo gerente e finalmente pelo funcionário para organizar o organograma de forma hierárquica.

Resultado Esperado:
Esse organograma deve listar cada gerente e, logo abaixo, os funcionários que reportam a ele, organizados por departamento. Se precisar de ajustes ou adicionar mais informações ao organograma, posso ajudar com modificações adicionais!

Aqui estão as respostas para cada exercício solicitado, utilizando as tabelas `hr.employees`, `hr.departments`, `hr.locations` e `hr.jobs`.

---

Mostre o total de linhas em cada Join entre a tabela hr.employees e hr.departments. Justifique o resultado.

Para isso, podemos realizar três tipos de `JOIN` e contar o total de linhas para cada um deles: `INNER JOIN`, `LEFT JOIN`, e `RIGHT JOIN`.

```sql
-- INNER JOIN
SELECT COUNT(*) AS total_inner_join
FROM hr.employees e
INNER JOIN hr.departments d ON e.department_id = d.department_id;

-- LEFT JOIN
SELECT COUNT(*) AS total_left_join
FROM hr.employees e
LEFT JOIN hr.departments d ON e.department_id = d.department_id;

-- RIGHT JOIN
SELECT COUNT(*) AS total_right_join
FROM hr.employees e
RIGHT JOIN hr.departments d ON e.department_id = d.department_id;
```

Justificativa:
1. **INNER JOIN**: Retorna apenas as linhas em que há correspondência entre `department_id` nas duas tabelas. O total de linhas será o número de funcionários com departamento atribuído.
2. **LEFT JOIN**: Retorna todas as linhas da tabela `hr.employees`, incluindo os departamentos correspondentes onde `department_id` coincide, e `NULL` onde não há correspondência.
3. **RIGHT JOIN**: Retorna todas as linhas da tabela `hr.departments`, incluindo os funcionários onde `department_id` coincide, e `NULL` para os departamentos sem funcionários.

---

1. Qual `state_province` tem mais funcionários?

Para encontrar o estado (`state_province`) com mais funcionários, fazemos uma `JOIN` entre as tabelas `hr.employees`, `hr.departments` e `hr.locations`.

```sql
SELECT l.state_province, COUNT(e.employee_id) AS total_funcionarios
FROM hr.employees e
JOIN hr.departments d ON e.department_id = d.department_id
JOIN hr.locations l ON d.location_id = l.location_id
GROUP BY l.state_province
ORDER BY total_funcionarios DESC
FETCH FIRST 1 ROWS ONLY;
```

Explicação:
- Agrupamos por `state_province` e contamos o total de funcionários em cada estado.
- Ordenamos de forma decrescente e selecionamos a primeira linha para encontrar o estado com mais funcionários.

---

2. Mostre o total de funcionários que têm o mesmo cargo e moram na mesma cidade.

```sql
SELECT j.job_title, l.city, COUNT(e.employee_id) AS total_funcionarios
FROM hr.employees e
JOIN hr.departments d ON e.department_id = d.department_id
JOIN hr.locations l ON d.location_id = l.location_id
JOIN hr.jobs j ON e.job_id = j.job_id
GROUP BY j.job_title, l.city
HAVING COUNT(e.employee_id) > 1;
```

Explicação:
- Agrupamos por `job_title` e `city`, contando os funcionários em cada combinação.
- Filtramos com `HAVING COUNT(e.employee_id) > 1` para mostrar apenas as combinações de cargo e cidade com mais de um funcionário.

---

3. Quantos gerentes (manager) por cidade tem nesta empresa?

```sql
SELECT l.city, COUNT(DISTINCT e.manager_id) AS total_gerentes
FROM hr.employees e
JOIN hr.departments d ON e.department_id = d.department_id
JOIN hr.locations l ON d.location_id = l.location_id
WHERE e.manager_id IS NOT NULL
GROUP BY l.city;
```

Explicação:
- Agrupamos por `city` e contamos os gerentes distintos (`manager_id`) em cada cidade.
- Utilizamos `WHERE e.manager_id IS NOT NULL` para contar apenas funcionários com um gerente atribuído.

---

4. Em média, são quantos funcionários por gerente? Qual gerente tem mais funcionários?

Para isso, usamos duas consultas: uma para a média de funcionários por gerente e outra para identificar o gerente com mais funcionários.

**Parte 1: Média de Funcionários por Gerente**

```sql
SELECT AVG(contagem_funcionarios) AS media_funcionarios_por_gerente
FROM (
    SELECT manager_id, COUNT(employee_id) AS contagem_funcionarios
    FROM hr.employees
    WHERE manager_id IS NOT NULL
    GROUP BY manager_id
);
```

**Parte 2: Gerente com Mais Funcionários**

```sql
SELECT manager_id, COUNT(employee_id) AS total_funcionarios
FROM hr.employees
WHERE manager_id IS NOT NULL
GROUP BY manager_id
ORDER BY total_funcionarios DESC
FETCH FIRST 1 ROWS ONLY;
```

Explicação:
1. **Média de Funcionários por Gerente**: A subconsulta agrupa os funcionários por `manager_id` e conta o número de funcionários para cada gerente. A consulta principal calcula a média dessas contagens.
2. **Gerente com Mais Funcionários**: Agrupamos por `manager_id` e contamos o número de funcionários para cada gerente. Ordenamos por `total_funcionarios` em ordem decrescente e selecionamos o primeiro resultado para identificar o gerente com mais funcionários. 

Essas consultas fornecem as respostas para os exercícios solicitados, utilizando `JOIN`, `GROUP BY`, `HAVING`, e outras técnicas SQL para analisar e extrair as informações.\


## DATES

### 1. Mostre o tempo de empresa de cada funcionário e crie uma coluna com 5 faixas de períodos.

```sql
SELECT 
    first_name,
    last_name,
    hire_date,
    TRUNC(MONTHS_BETWEEN(SYSDATE, hire_date) / 12) AS anos_empresa,
    CASE 
        WHEN MONTHS_BETWEEN(SYSDATE, hire_date) <= 12 THEN 'Até 1 ano'
        WHEN MONTHS_BETWEEN(SYSDATE, hire_date) <= 36 THEN '1-3 anos'
        WHEN MONTHS_BETWEEN(SYSDATE, hire_date) <= 60 THEN '3-5 anos'
        WHEN MONTHS_BETWEEN(SYSDATE, hire_date) <= 120 THEN '5-10 anos'
        ELSE 'Mais de 10 anos'
    END AS faixa_tempo_empresa
FROM hr.employees;
```

---

### 2. Mostre em dias, meses e anos há quanto tempo cada funcionário deixou a empresa

Para este exercício, precisamos verificar a tabela `hr.job_history` para calcular o tempo desde a saída do funcionário.

```sql
SELECT 
    employee_id,
    end_date,
    ROUND(SYSDATE - end_date) AS dias_apos_saida,
    TRUNC(MONTHS_BETWEEN(SYSDATE, end_date)) AS meses_apos_saida,
    TRUNC(MONTHS_BETWEEN(SYSDATE, end_date) / 12) AS anos_apos_saida
FROM hr.job_history
WHERE end_date IS NOT NULL;
```

---

### 3. Calcule a diferença de tempo (em dias, meses e anos) entre os funcionários com mais e menos tempo de empresa (entrada e saída)

```sql
WITH MinMaxHire AS (
    SELECT 
        MIN(hire_date) AS data_contratacao_mais_antiga,
        MAX(hire_date) AS data_contratacao_mais_recente
    FROM hr.employees
)
SELECT 
    data_contratacao_mais_antiga,
    data_contratacao_mais_recente,
    data_contratacao_mais_recente - data_contratacao_mais_antiga AS dias_diferenca,
    TRUNC(MONTHS_BETWEEN(data_contratacao_mais_recente, data_contratacao_mais_antiga)) AS meses_diferenca,
    TRUNC(MONTHS_BETWEEN(data_contratacao_mais_recente, data_contratacao_mais_antiga) / 12) AS anos_diferenca
FROM MinMaxHire;
```

---

### 4. Calcule a idade de empresa (considerando sua criação como a data de contratação do primeiro funcionário)

```sql
SELECT 
    MIN(hire_date) AS data_fundacao,
    SYSDATE AS data_atual,
    SYSDATE - MIN(hire_date) AS idade_dias,
    TRUNC(MONTHS_BETWEEN(SYSDATE, MIN(hire_date))) AS idade_meses,
    TRUNC(MONTHS_BETWEEN(SYSDATE, MIN(hire_date)) / 12) AS idade_anos
FROM hr.employees;
```

---

### 5. Calcule a média salarial dos funcionários que foram contratados no mesmo mês e ano.

```sql
SELECT 
    EXTRACT(YEAR FROM hire_date) AS ano_contratacao,
    EXTRACT(MONTH FROM hire_date) AS mes_contratacao,
    AVG(salary) AS media_salario
FROM hr.employees
GROUP BY EXTRACT(YEAR FROM hire_date), EXTRACT(MONTH FROM hire_date);
```

---

### 6. Mostre em qual mês e ano houveram mais contratações.

```sql
SELECT 
    EXTRACT(YEAR FROM hire_date) AS ano_contratacao,
    EXTRACT(MONTH FROM hire_date) AS mes_contratacao,
    COUNT(*) AS total_contratacoes
FROM hr.employees
GROUP BY EXTRACT(YEAR FROM hire_date), EXTRACT(MONTH FROM hire_date)
ORDER BY total_contratacoes DESC
FETCH FIRST 1 ROWS ONLY;
```

---

### 7. Considerando o funcionário com mais tempo de empresa, quantas contratações aconteceram durante sua jornada? E do funcionário com menos tempo? Mostre a diferença dos valores encontrados.

```sql
WITH ContratacaoExtremos AS (
    SELECT 
        MIN(hire_date) AS data_mais_antiga,
        MAX(hire_date) AS data_mais_recente
    FROM hr.employees
)
SELECT 
    (SELECT COUNT(*) FROM hr.employees WHERE hire_date >= (SELECT data_mais_antiga FROM ContratacaoExtremos)) AS contratacoes_apos_mais_antigo,
    (SELECT COUNT(*) FROM hr.employees WHERE hire_date >= (SELECT data_mais_recente FROM ContratacaoExtremos)) AS contratacoes_apos_mais_recente,
    (SELECT COUNT(*) FROM hr.employees WHERE hire_date >= (SELECT data_mais_antiga FROM ContratacaoExtremos)) -
    (SELECT COUNT(*) FROM hr.employees WHERE hire_date >= (SELECT data_mais_recente FROM ContratacaoExtremos)) AS diferenca_contratacoes;
```

---

### 8. Funcionários com mais tempo de empresa, têm salários acima da média geral? E da média por departamento?

```sql
WITH MediaSalarial AS (
    SELECT 
        AVG(salary) AS media_salarial_geral
    FROM hr.employees
)
SELECT 
    e.first_name, e.last_name, e.salary, e.hire_date,
    d.department_name,
    CASE 
        WHEN e.salary > (SELECT media_salarial_geral FROM MediaSalarial) THEN 'Acima da média geral'
        ELSE 'Abaixo da média geral'
    END AS comparacao_media_geral,
    CASE 
        WHEN e.salary > (SELECT AVG(salary) FROM hr.employees WHERE department_id = e.department_id) THEN 'Acima da média do departamento'
        ELSE 'Abaixo da média do departamento'
    END AS comparacao_media_departamento
FROM hr.employees e
JOIN hr.departments d ON e.department_id = d.department_id
WHERE e.hire_date = (SELECT MIN(hire_date) FROM hr.employees);
```

---

### 9. Inclua uma coluna com as classificações: mais do que 2 desvios-padrão abaixo da média, 1 desvio-padrão abaixo da média, na média, 1 desvio-padrão acima da média, mais do que 2 desvios-padrão acima da média

```sql
WITH EstatisticasSalario AS (
    SELECT 
        AVG(salary) AS media_salario,
        STDDEV(salary) AS desvio_padrao
    FROM hr.employees
)
SELECT 
    first_name,
    last_name,
    salary,
    CASE 
        WHEN salary < (SELECT media_salario - 2 * desvio_padrao FROM EstatisticasSalario) THEN 'Mais de 2 desvios abaixo da média'
        WHEN salary < (SELECT media_salario - desvio_padrao FROM EstatisticasSalario) THEN '1 desvio abaixo da média'
        WHEN salary BETWEEN (SELECT media_salario - desvio_padrao FROM EstatisticasSalario) AND (SELECT media_salario + desvio_padrao FROM EstatisticasSalario) THEN 'Na média'
        WHEN salary > (SELECT media_salario + desvio_padrao FROM EstatisticasSalario) AND salary <= (SELECT media_salario + 2 * desvio_padrao FROM EstatisticasSalario) THEN '1 desvio acima da média'
        ELSE 'Mais de 2 desvios acima da média'
    END AS classificacao_salario
FROM hr.employees;
```

Essas consultas cobrem os exercícios utilizando funções de manipulação de datas, cálculos com períodos de tempo, e classificação de dados com `CASE WHEN`. Se precisar de mais ajustes ou explicações, estou à disposição!

## UNION
Vamos resolver os exercícios propostos usando o que aprendemos sobre **funções de janela** e consultas com **ranking** e **diferenças de datas** na tabela `hr.employees`.

---

### 1) Se fizer um ranking dos salários, qual posição tem mais funcionários?

Para responder a essa pergunta, vamos classificar os funcionários por salário usando `DENSE_RANK` e, em seguida, contar quantos funcionários estão em cada posição do ranking.

```sql
WITH SalarioRanking AS (
    SELECT 
        salary,
        DENSE_RANK() OVER (ORDER BY salary DESC) AS posicao_ranking
    FROM hr.employees
)
SELECT 
    posicao_ranking,
    COUNT(*) AS total_funcionarios
FROM SalarioRanking
GROUP BY posicao_ranking
ORDER BY total_funcionarios DESC
FETCH FIRST 1 ROWS ONLY;
```

### Explicação:
- A CTE `SalarioRanking` classifica os salários usando `DENSE_RANK` para que cada posição única no ranking corresponda a um único valor de salário.
- A consulta principal conta quantos funcionários estão em cada posição e seleciona a posição com o maior número de funcionários.

---

### 2) Mostre o nome do funcionário e a diferença salarial deste funcionário com o contratado na data anterior

Para calcular a diferença salarial entre um funcionário e o contratado anterior, podemos usar a função `LAG` para obter o salário do contratado anterior.

```sql
SELECT 
    first_name,
    last_name,
    salary,
    hire_date,
    salary - LAG(salary) OVER (ORDER BY hire_date) AS diferenca_salario_com_anterior
FROM hr.employees
ORDER BY hire_date;
```

### Explicação:
- **`LAG(salary) OVER (ORDER BY hire_date)`**: Obtém o salário do funcionário contratado imediatamente antes.
- **`salary - LAG(salary) ...`**: Calcula a diferença entre o salário do funcionário atual e o contratado anterior.

---

### 3) Mostre a diferença média de dias entre as contratações

Para calcular a diferença média de dias entre contratações, podemos usar `LAG` para calcular o número de dias entre cada contratação e depois calcular a média dessas diferenças.

```sql
WITH DiferencaDatas AS (
    SELECT 
        hire_date,
        LAG(hire_date) OVER (ORDER BY hire_date) AS data_contratacao_anterior,
        hire_date - LAG(hire_date) OVER (ORDER BY hire_date) AS diferenca_dias
    FROM hr.employees
)
SELECT 
    AVG(diferenca_dias) AS media_diferenca_dias
FROM DiferencaDatas
WHERE diferenca_dias IS NOT NULL;
```

### Explicação:
- A CTE `DiferencaDatas` calcula a diferença em dias entre cada data de contratação e a contratação anterior.
- A consulta principal calcula a média dessas diferenças.

---

### 4) Mostre a diferença média de dias entre as contratações considerando o ranking salarial (apenas dos salários únicos, isto é, sem empate no ranking)

Aqui, vamos primeiro classificar os funcionários por salário usando `DENSE_RANK` para eliminar empates e, em seguida, calcular a diferença média de dias entre contratações para esses salários únicos.

```sql
WITH SalariosUnicos AS (
    SELECT 
        salary,
        hire_date,
        DENSE_RANK() OVER (ORDER BY salary DESC) AS ranking_salarial
    FROM hr.employees
),
DiferencaDatas AS (
    SELECT 
        hire_date,
        LAG(hire_date) OVER (ORDER BY hire_date) AS data_contratacao_anterior,
        hire_date - LAG(hire_date) OVER (ORDER BY hire_date) AS diferenca_dias
    FROM SalariosUnicos
)
SELECT 
    AVG(diferenca_dias) AS media_diferenca_dias_ranking
FROM DiferencaDatas
WHERE diferenca_dias IS NOT NULL;
```

### Explicação:
- A CTE `SalariosUnicos` classifica os salários usando `DENSE_RANK` para garantir que consideramos apenas valores únicos de salário (sem empates).
- A CTE `DiferencaDatas` calcula a diferença de dias entre as datas de contratação para os salários classificados.
- A consulta principal calcula a média dessas diferenças de dias.

Essas consultas atendem aos requisitos dos exercícios, utilizando funções de janela para calcular rankings e diferenças de datas, além de aplicar o conceito de salários únicos para o último exercício.

# 💾 Integrando Dados com MySQL Azure e Transformando com Power BI

## 📚 Descrição do Desafio
Este projeto faz parte do curso de **Power BI com Azure e MySQL** da [Digital Innovation One (DIO)](https://www.dio.me/).  
O objetivo é **criar uma instância MySQL na Azure**, hospedar um banco de dados relacional e realizar a **integração e transformação de dados no Power BI**, aplicando práticas de modelagem e limpeza de dados.

---

## 🧭 Etapas do Projeto

### 🏗️ 1. Criação do Banco de Dados na Azure
- Acesse o portal [Azure](https://portal.azure.com/)
- Crie um recurso do tipo **Banco de Dados para MySQL – Servidor Flexível**
- Configure:
  - **Usuário e senha administrativos**
  - **Camada básica** (para fins de aprendizado)
  - **Regras de firewall**: adicione o IP local para permitir acesso externo

---

### ⚙️ 2. Criação e Importação da Base de Dados
- Baixe a base SQL no GitHub do curso (arquivo `.sql` com as tabelas `employee`, `department`, `project`, etc.)
- Conecte-se via **MySQL Workbench**
- Execute o script para criar o banco e popular as tabelas

---

### ☁️ 3. Conexão do Power BI com MySQL Azure
1. No Power BI Desktop, clique em **Obter Dados → MySQL Database**
2. Informe o **servidor (ex: servidor.mysql.database.azure.com)** e **credenciais**
3. Selecione as tabelas desejadas (ex: `employee`, `department`, `project`, `works_on`)
4. Clique em **Transformar Dados** para abrir o Power Query Editor

---

## 🧩 4. Diretrizes de Transformação dos Dados

Durante o processo de transformação no Power Query, foram aplicadas as seguintes etapas:

| Etapa | Ação | Descrição |
|-------|------|------------|
| 1️⃣ | Verificação de Cabeçalhos | Padronização dos nomes das colunas e tipos de dados |
| 2️⃣ | Conversão de Tipos Monetários | Campos de salário e valores monetários convertidos para tipo **Decimal (double preciso)** |
| 3️⃣ | Tratamento de Valores Nulos | Análise de campos nulos em `Super_ssn` e `Mgr_ssn` |
| 4️⃣ | Gerentes Nulos | Identificados como colaboradores sem gerente; considerados **nível gerencial** |
| 5️⃣ | Departamentos sem Gerente | Lacunas preenchidas manualmente conforme regra de negócio |
| 6️⃣ | Validação de Horas dos Projetos | Conferência de valores inconsistentes em `Works_On` |
| 7️⃣ | Separação de Colunas Complexas | Normalização de colunas concatenadas (ex: Nome Completo, Endereço) |
| 8️⃣ | Mescla Employee + Department | Junção pelo campo `Dno` para incluir nome do departamento nos colaboradores |
| 9️⃣ | Mescla com Gerentes | Junção `Employee.Ssn` ↔ `Department.Mgr_ssn` para vincular nomes de gerentes |
| 🔟 | Coluna Nome Completo | Mesclagem de `Fname` + `Lname` em uma única coluna `Employee Name` |
| 11️⃣ | Mescla Departamento + Localização | Criação de coluna única `Department-Location` |
| 12️⃣ | Agrupamento | Total de colaboradores por gerente via **Group By (Power Query)** |
| 13️⃣ | Remoção de Colunas Inúteis | Exclusão de campos redundantes pós-transformação |

---

## 🧮 5. Modelo Final

Após as transformações, o modelo de dados foi estruturado da seguinte forma:


- `Employee`: contém nome completo, cargo, gerente e departamento  
- `Department`: contém nome, localização e gerente  
- `Project`: contém nome e número do projeto  
- `Works_On`: relaciona funcionários e projetos, com horas trabalhadas  

---

## 🧠 Explicações Importantes

**🔹 Por que usamos “Mesclar” em vez de “Atribuir”?**  
No contexto do Power Query, **Mesclar (Merge)** é usado para **combinar dados de duas tabelas em uma única consulta**, preservando o relacionamento.  
O **Atribuir (Append)** é usado para **concatenar linhas de tabelas com a mesma estrutura** — o que não se aplica neste caso, pois queremos unir colunas complementares.

---

## 🧰 Ferramentas Utilizadas

| Ferramenta | Finalidade |
|-------------|-------------|
| ☁️ **Microsoft Azure** | Hospedagem do banco MySQL |
| 🐬 **MySQL Workbench** | Criação e manipulação do banco de dados |
| 📊 **Power BI Desktop** | Integração, transformação e visualização dos dados |
| 🧱 **Power Query Editor** | Limpeza e modelagem dos dados |
| 💻 **GitHub** | Versionamento e entrega do projeto |

---

## 📎 Estrutura do Repositório

📁 azure_mysql_powerbi/
├── database/
│ └── base_dados.sql
├── transform/
│ └── modelo_transformado.pbix
├── docs/
│ └── prints_etapas.pdf
└── README.md

---

## 🧾 Exemplo de Query (para junção via SQL)

```sql
SELECT 
    e.Fname AS EmployeeName,
    e.Lname AS EmployeeLastName,
    d.Dname AS Department,
    m.Fname AS ManagerName
FROM employee e
JOIN department d ON e.Dno = d.Dnumber
LEFT JOIN employee m ON d.Mgr_ssn = m.Ssn;

# Upgrade do MySQL 5.7 para 8.0

Este documento descreve o processo passo a passo para realizar o upgrade do MySQL da versão 5.7 para 8.0.

## Passo a Passo

### 0. Restringir Acesso
Alocar um **Security Group** com acesso **NEGADO** para todos no banco de produção.

### 1. Criar um Snapshot Manual
Criar um snapshot **manual** do banco atual para permitir a alteração do tipo de capacidade ao restaurá-lo. Durante a restauração, utilize o tipo de capacidade **"provisionado"**.

### 2. Criar um Novo Parameter Group
Criar um novo **Parameter Group** com a opção de **case sensitive desligada**, caso seja uma necessidade do cliente.

### 3. Restaurar Snapshot na Versão 5.7 e Corrigir Índices
Restaurar o snapshot utilizando a versão **5.7** do MySQL. Em seguida, acessar o banco e corrigir possíveis problemas de índice.

### 4. Corrigir Problemas de Índice
Executar a seguinte query para corrigir problemas de índice **em todos os databases** (substituir `schema.database` e `column1, column2, column3` pelos respectivos nomes):

```sql
ALTER TABLE schema.database DROP INDEX IDX_FULLTEXT;
ALTER TABLE schema.database ADD FULLTEXT INDEX IDX_FULLTEXT (column1, column2, column3);
```

### 5. Ajustar Tempo de Conexão
Alterar o tempo máximo de conexão no MySQL para garantir suporte à atualização do **Passo 7**. Para isso, siga as instruções disponíveis nesta documentação:
[MySQL Workbench - How to Keep the Connection Alive](https://stackoverflow.com/questions/15712512/mysql-workbench-how-to-keep-the-connection-alive)

### 6. Corrigir Problemas de UTF-8
Para corrigir problemas relacionados ao `utf8`, executar as queries abaixo:

```sql
USE schema;

ALTER TABLE database
MODIFY arquivo VARCHAR(255)
CHARACTER SET utf8mb4;

ALTER DATABASE nomedobanco CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
```

### 7. Upgrade para o MySQL 8.0
No momento da atualização para a versão **8.0**, certifique-se de utilizar o novo **Parameter Group** criado no Passo 2.

---

Após seguir estes passos, seu banco de dados estará atualizado para o MySQL 8.0 com os ajustes necessários para evitar problemas de compatibilidade.


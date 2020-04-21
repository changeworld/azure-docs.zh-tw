---
title: 管理工作區、資料和管線的存取權
description: 了解如何在 Azure Synapse Analytics 工作區 (預覽) 中管理工作區、資料和管線的存取控制。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 89d2105ab080309639c4341072c3f5f36608dfce
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421102"
---
# <a name="manage-access-to-workspaces-data-and-pipelines"></a>管理工作區、資料和管線的存取權

了解如何在 Azure Synapse Analytics 工作區 (預覽) 中管理工作區、資料和管線的存取控制。

> [!NOTE]
> 針對 GA，RBAC 會透過引進 Synapse 特有的 Azure RBAC 角色而完成更進一步的開發

## <a name="access-control-for-workspace"></a>工作區的存取控制

若要將生產環境部署到 Azure Synapse 工作區，建議您組織環境，以便讓使用者和系統管理員的佈建變得更容易。

> [!NOTE]
> 這裡所採用的方法是建立數個安全性群組，然後設定工作區以便以一致的方式使用這些群組。 群組完成設定後，系統管理員就只需要管理安全性群組內的成員資格。

### <a name="step-1-set-up-security-groups-with-names-following-this-pattern"></a>步驟 1:以遵循下列模式的名稱來設定安全性群組

1. 建立名為 `Synapse_WORKSPACENAME_Users` 的安全性群組
2. 建立名為 `Synapse_WORKSPACENAME_Admins` 的安全性群組
3. 將 `Synapse_WORKSPACENAME_Admins` 新增至 `ProjectSynapse_WORKSPACENAME_Users`

### <a name="step-2-prepare-the-default-adls-gen2-account"></a>步驟 2:準備預設 ADLS Gen2 帳戶

佈建了工作區之後，就必須挑選檔案系統的 ADLSGEN2 帳戶和容器以供工作區使用。

1. 開啟 [Azure 入口網站](https://portal.azure.com)
2. 瀏覽至 ADLSGEN2 帳戶
3. 瀏覽至您為 Azure Synapse 工作區挑選的容器 (檔案系統)
4. 按一下 [存取控制 (IAM)] 
5. 指派下列角色：
   1. **讀者**角色：`Synapse_WORKSPACENAME_Users`
   2. **儲存體 Blob 資料擁有者**角色：`Synapse_WORKSPACENAME_Admins`
   3. **儲存體 Blob 資料參與者**角色：`Synapse_WORKSPACENAME_Users`
   4. **儲存體 Blob 資料擁有者**角色：`WORKSPACENAME`
  
### <a name="step-3-configure-the-workspace-admin-list"></a>步驟 3：設定工作區系統管理員清單

1. 移至 [**Azure Synapse Web UI**](https://web.azuresynapse.net)
2. 移至 [管理]    > [安全性]   > [存取控制] 
3. 按一下 [新增系統管理員]  ，然後選取 `Synapse_WORKSPACENAME_Admins`

### <a name="step-4-configure-sql-admin-access-for-the-workspace"></a>步驟 4：設定工作區的 SQL 系統管理員存取權

1. 移至 [Azure 入口網站](https://portal.azure.com)
2. 瀏覽至您的工作區
3. 移至 [設定]   > [Active Directory 系統管理員] 
4. 按一下 [設定系統管理員] 
5. 選取 `Synapse_WORKSPACENAME_Admins`
6. 按一下 [選取] 
7. 按一下 [儲存] 

### <a name="step-5-add-and-remove-users-and-admins-to-security-groups"></a>步驟 5：在安全性群組中新增和移除使用者與系統管理員

1. 將需要系統管理存取權的使用者新增至 `Synapse_WORKSPACENAME_Admins`
2. 將所有其他使用者新增至 `Synapse_WORKSPACENAME_Users`

## <a name="access-control-to-data"></a>資料的存取控制

基礎資料的存取控制分為三個部分：

- 儲存體帳戶的資料平面存取權 (已在上面的步驟 2 完成設定)
- SQL Database 的資料平面存取權 (同時適用於 SQL 集區和 SQL 隨選)
- 透過儲存體帳戶建立 SQL 隨選資料庫的認證

## <a name="access-control-to-sql-databases"></a>SQL Database 的存取控制

> [!TIP]
> 您必須針對**每個** SQL 資料庫執行下列步驟，以便對使用者授與所有 SQL 資料庫的存取權。

### <a name="sql-on-demand"></a>SQL 隨選

若要對使用者授與**單一** SQL 隨選資料庫的存取權，請遵循此範例中的步驟：

1. 建立登入

    ```sql
    use master
    go
    CREATE LOGIN [John.Thomas@microsoft.com] FROM EXTERNAL PROVIDER;
    go
    ```

2. 建立使用者

    ```sql
    use yourdb -- Use your DB name
    go
    CREATE USER john FROM LOGIN [John.Thomas@microsoft.com];
    ```

3. 將使用者新增至指定角色的成員

    ```sql
    use yourdb -- Use your DB name
    go
    alter role db_owner Add member john -- Type USER name from step 2
    ```

### <a name="sql-pools"></a>SQL 集區

若要對使用者授與**單一** SQL Database 的存取權，請遵循下列步驟：

1. 藉由在內容選取器 (用來選取資料庫的下拉式清單) 中執行下列以所需資料庫為目標的命令，於資料庫中建立使用者：

    ```sql
    --Create user in SQL DB
    CREATE USER [<alias@domain.com>] FROM EXTERNAL PROVIDER;
    ```

2. 對使用者授與可存取資料庫的角色：

    ```sql
    --Create user in SQL DB
    EXEC sp_addrolemember 'db_owner', '<alias@domain.com>';
    ```

> [!IMPORTANT]
> 如果您不想要授與 *db_owner* 權限，則 *db_datareader* 和 *db_datawriter* 可適用於讀取/寫入權限。
> 若要讓 Spark 使用者能夠直接從 Spark 讀取和寫入至 SQL 集區 (反之亦然)，則需要有 *db_owner* 權限。

在建立使用者後，請驗證 SQL 隨選是否可查詢儲存體帳戶：

- 執行下列以 SQL 隨選 **master** 資料庫為目標的命令：

    ```sql
    CREATE CREDENTIAL [https://<storageaccountname>.dfs.core.windows.net]
    WITH IDENTITY='User Identity';
    ```

## <a name="access-control-to-workspace-pipeline-runs"></a>工作區管線執行的存取控制

### <a name="workspace-managed-identity"></a>工作區受控識別

> [!IMPORTANT]
> 若要成功執行參考 SQL 集區的資料集或活動所在的管線，您必須對工作區身分識別直接授與 SQL 集區的存取權。

在每個 SQL 集區上執行下列命令，以允許工作區受控識別在 SQL 集區資料庫上執行管線：

```sql
--Create user in DB
CREATE USER [<workspacename>] FROM EXTERNAL PROVIDER;

--Granting permission to the identity
GRANT CONTROL ON DATABASE::<SQLpoolname> TO <workspacename>;
```

在相同的 SQL 集區上執行下列指令碼，即可移除此權限：

```sql
--Revoking permission to the identity
REVOKE CONTROL ON DATABASE::<SQLpoolname> TO <workspacename>;

--Deleting the user in the DB
DROP USER [<workspacename>];
```

## <a name="next-steps"></a>後續步驟

如需 Synapse SQL 中的存取和控制概觀，請參閱 [Synapse SQL 存取控制](../sql/access-control.md)。 若要深入了解資料庫主體，請參閱[主體](https://msdn.microsoft.com/library/ms181127.aspx)。 如需資料庫角色的其他資訊，請參閱[資料庫角色](https://msdn.microsoft.com/library/ms189121.aspx)一文。

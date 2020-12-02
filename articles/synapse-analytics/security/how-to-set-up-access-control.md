---
title: 如何設定 Synapse 工作區的存取控制
description: 本文將告訴您如何使用 Azure 角色、Synapse 角色、SQL 許可權和 Git 許可權來控制對 Synapse 工作區的存取。
services: synapse-analytics
author: billgib
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/03/2020
ms.author: billgib
ms.reviewer: jrasnick
ms.openlocfilehash: 7243d24204c8e15ae4246718cafb24d31f804d02
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96519173"
---
# <a name="how-to-set-up-access-control-for-your-synapse-workspace"></a>如何設定 Synapse 工作區的存取控制 

本文將告訴您如何使用 Azure 角色、Synapse 角色、SQL 許可權和 Git 許可權來控制對 Synapse 工作區的存取。   

在本指南中，您將設定工作區，並設定適用于許多 Synapse 專案的基本存取控制系統。  然後，它會針對您需要的更精細的控制，說明更先進的選項。  

您可以使用與組織中角色和角色一致的安全性群組，來簡化 Synapse 存取控制。  您只需要新增和移除安全性群組中的使用者，即可管理存取權。

開始本逐步解說之前，請閱讀 [Synapse 存取控制的總覽](./synapse-workspace-access-control-overview.md) ，以熟悉 Synapse 所使用的存取控制機制。   

## <a name="access-control-mechanisms"></a>存取控制機制

> [!NOTE]
> 本指南所採用的方法是建立數個安全性群組，然後將角色指派給這些群組。 設定群組之後，您只需要管理安全性群組內的成員資格，即可控制工作區的存取權。

若要保護 Synapse 工作區，您將遵循設定下列專案的模式：

- **安全性群組**，用來將具有類似存取需求的使用者分組。
- **Azure 角色**，可控制誰可以建立和管理 SQL 集區、Apache Spark 集區和整合執行時間，以及存取 ADLS Gen2 儲存體。
- **Synapse 角色**，以控制對已發佈程式碼成品的存取，使用 Apache Spark 計算資源和整合執行時間 
- **Sql 許可權**，用來控制 sql 集區的系統管理和資料平面存取權。 
- **Git 許可權**，可在您為工作區設定 Git 支援時，控制誰可以存取原始檔控制中的程式碼成品 
 
## <a name="steps-to-secure-a-synapse-workspace"></a>保護 Synapse 工作區的步驟

本文件會使用標準名稱來簡化指示。 以您選擇的名稱取代它們。

|設定 | 標準名稱 | 描述 |
| :------ | :-------------- | :---------- |
| **Synapse 工作區** | `workspace1` |  Synapse 工作區將會擁有的名稱。 |
| **ADLSGEN2 帳戶** | `storage1` | 要與您工作區搭配使用的 ADLS 帳戶。 |
| **容器** | `container1` | STG1 中工作區預設會使用的容器。 |
| **Active Directory 租用戶** | `contoso` | Active Directory 租用戶名稱。|
||||

## <a name="step-1-set-up-security-groups"></a>步驟 1：設定安全性群組

>[!Note] 
>在預覽期間，建議您建立對應到 Synapse **SYNAPSE SQL 系統管理員** 和 **Synapse Apache Spark 系統管理員** 角色的安全性群組。  隨著新的更精細 Synapse RBAC 角色和範圍的推出，現在建議您使用這些新功能來控制您工作區的存取權。  這些新的角色和範圍提供更多的設定彈性，並可辨識開發人員通常會混合使用 SQL 和 Spark 來建立分析應用程式，而且可能需要授與工作區中特定資源的存取權。 [深入了解](./synapse-workspace-synapse-rbac.md)。

為您的工作區建立下列安全性群組：

- **`workspace1_SynapseAdministrators`**，適用于需要完整控制工作區的使用者。  將您自己新增到這個安全性群組（至少一開始）。
- **`workspace1_SynapseContributors`**，適用于需要開發、偵錯工具代碼並將其發行至服務的開發人員。   
- **`workspace1_SynapseComputeOperators`**，適用于需要管理和監視 Apache Spark 集區和整合執行時間的使用者。
- **`workspace1_SynapseCredentialUsers`** 對於需要使用工作區 MSI 來偵測和執行協調流程管線 (受控服務識別) 認證和取消管線執行的使用者。   

您稍後會在工作區範圍將 Synapse 角色指派給這些群組。  

也請建立此安全性群組： 
- **`workspace1_SQLAdministrators`**，在工作區的 SQL 集區中需要 Active Directory 系統管理員授權單位的使用者群組。 

`workspace1_SynapseSQLAdministrators`當您建立 sql 集區中的 sql 許可權時，將會使用此群組。 

針對基本設定，這五個群組就已足夠。 稍後，您可以新增安全性群組來處理需要更特製化存取權的使用者，或只授與使用者特定資源的存取權。

> [!NOTE]
>- 從[此文章](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)可了解如何建立安全性群組。
>- 從[此文章](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-membership-azure-portal)可了解如何從另一個安全性群組新增安全性群組。

>[!Tip]
>個別的 Synapse 使用者可以使用 Azure 入口網站中的 Azure Active Directory 來查看其群組成員資格，以判斷他們被授與的角色。

## <a name="step-2-prepare-your-adls-gen2-storage-account"></a>步驟2：準備您的 ADLS Gen2 儲存體帳戶

Synapse 工作區會使用的預設儲存體容器：
  - 儲存 Spark 資料表的支援資料檔案
  - Spark 作業的執行記錄

識別儲存體的下列資訊：

- 要用於您工作區的 ADLS Gen2 帳戶。 這份檔會呼叫它 `storage1` 。 `storage1` 會被視為您工作區的「主要」儲存體帳戶。
- `workspace1`Synapse 工作區中預設會使用的容器。 這份檔會呼叫它 `container1` 。 

- 使用 Azure 入口網站，將下列 Azure 角色指派 `container1` 給安全性群組 

  - 將 **儲存體 Blob 資料參與者** 角色指派給 `workspace1_SynapseAdmins` 
  - 將 **儲存體 Blob 資料參與者** 角色指派給 `workspace1_SynapseContributors`
  - 將 **儲存體 Blob 資料參與者** 角色指派給 `workspace1_SynapseComputeOperators` **<< 驗證**  

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>步驟 3：建立和設定您的 Synapse 工作區

在 Azure 入口網站中，建立 Synapse 工作區：

- 選取您的訂用帳戶
- 選取或建立您擁有 Azure **擁有** 者角色的資源群組。
- 命名工作區 `workspace1`
- 選擇 `storage1` 儲存體帳戶
- 選擇 `container1` 作為 "filesystem" 使用的容器。
- 在 Synapse Studio 中開啟 WS1
- 流覽以 **管理**  >  **存取控制**，並將 *工作區範圍* 的下列 Synapse 角色指派給安全性群組。
  - 將 **Synapse 系統管理員** 角色指派給 `workspace1_SynapseAdministrators` 
  - 將 **Synapse 參與者** 角色指派給 `workspace1_SynapseContributors` 
  - 將 **SYNAPSE SQL 計算操作員** 角色指派給 `workspace1_SynapseComputeOperators`

## <a name="step-4-grant-the-workspace-msi-access-to-the-default-storage-container"></a>步驟4：將工作區 MSI 存取權授與預設儲存體容器

若要執行管線並執行系統工作，Synapse 需要 (MSI) 的工作區管理服務識別需要存取 `container1` 預設 ADLS Gen2 帳戶中的。

- 開啟 Azure 入口網站
- 找出儲存體帳戶、 `storage1` ，然後 `container1`
- 使用「 **存取控制」 (IAM)**，請確定已將「 **儲存體 Blob 資料參與者** 」角色指派給工作區 MSI
  - 如果未指派，請將它指派。
  - MSI 的名稱與工作區相同。 在本文中，它會是 `workspace1` 。

## <a name="step-5-grant-the-synapse-administrators-the-azure-contributor-role-on-the-workspace"></a>步驟5：將工作區的 Azure 參與者角色授與 Synapse 系統管理員 

若要建立 SQL 集區、Apache Spark 集區和整合執行時間，使用者必須至少擁有工作區的 Azure 參與者存取權。 「參與者」角色也可讓這些使用者管理資源，包括暫停和調整。

- 開啟 Azure 入口網站
- 找出工作區 `workspace1`
- 將上的「 **Azure 參與者** 」角色指派 `workspace1` 給 `workspace1_SynapseAdministrators` 。 

## <a name="step-6-assign-sql-active-directory-admin-role"></a>步驟6：指派 SQL Active Directory 管理員角色

工作站建立者會自動設定為工作區的 Active Directory 系統管理員。  只有單一使用者或群組可以被授與此角色。 在此步驟中，您會將工作區上的 Active Directory 系統管理員指派給 `workspace1_SynapseSQLAdministrators` 安全性群組。  指派此角色可讓此群組具有高度許可權的系統管理員存取權，以存取所有 SQL 集區。   

- 開啟 Azure 入口網站
- 巡覽到 `workspace1`
- 在 [**設定**] 底下，選取 **[SQL Active Directory 管理員**]
- 選取 [ **設定管理員** ] 並選擇 **`workspace1_SynapseSQLAdministrators`**

>[!Note]
>此為選用步驟。  您可以選擇授與 SQL 系統管理員群組較不具特殊許可權的角色。 若要指派 `db_owner` 或其他 sql 角色，您必須在每個 sql database 上執行腳本。 

## <a name="step-7-grant-access-to-sql-pools"></a>步驟7：授與 SQL 集區的存取權

依預設，指派 Synapse 系統管理員角色的所有使用者也會在 `db_owner` 無伺服器 sql 集區「內建」上指派 SQL 角色。

存取其他使用者和工作區 MSI 的 SQL 集區時，是使用 SQL 許可權來控制。  指派 SQL 許可權需要在建立之後于每個 SQL 集區上執行 SQL 腳本。  有三種情況需要您執行這些腳本：
1. 將無伺服器 SQL 集區的存取權授與其他使用者「內建」
2. 將專用集區的存取權授與任何使用者
3. 將工作區 MSI 存取權授與 SQL 集區，以啟用需要 SQL 集區存取的管線才能成功執行。

範例 SQL 腳本包含在下方。

若要授與存取權給專用的 SQL 集區，可以由工作區建立者或群組的任何成員來執行腳本 `workspace1_SynapseSQL Administrators` 。  

若要授與無伺服器 SQL 集區「內建」的存取權，您可以另外由群組的任何成員執行腳本  `workspace1_SynapseAdministrators` 。 

> [!TIP]
> 您必須針對 **每個** sql 集區執行下列步驟，以授與使用者存取所有 sql 資料庫的許可權，但您可以為其指派系統管理員（sysadmin）角色的部分 [工作區範圍許可權](#workspace-scoped-permission) 。

### <a name="step-71-serverless-sql-pools"></a>步驟7.1：無伺服器 SQL 集區

在本節中，您可以找到有關如何授與使用者特定資料庫或完整伺服器許可權的許可權範例。

> [!NOTE]
> 在腳本範例中，將 *alias* 取代為要授與存取權的使用者或群組的別名，以及使用您所使用之公司網域的 *網域* 。

#### <a name="pool-scoped-permission"></a>集區範圍許可權

若要將使用者的存取權授與 **單一** 無伺服器 SQL 集區，請遵循此範例中的步驟：

1. 建立登入

    ```sql
    use master
    go
    CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
    go
    ```

2. 建立使用者

    ```sql
    use yourdb -- Use your DB name
    go
    CREATE USER alias FROM LOGIN [alias@domain.com];
    ```

3. 將使用者新增至指定角色的成員

    ```sql
    use yourdb -- Use your DB name
    go
    alter role db_owner Add member alias -- Type USER name from step 2
    ```

#### <a name="workspace-scoped-permission"></a>工作區範圍許可權

若要將完整存取權授與工作區中的 **所有** 無伺服器 SQL 集區，請使用此範例中的腳本：

```sql
CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
ALTER SERVER ROLE  sysadmin  ADD MEMBER [alias@domain.com];
```

### <a name="step-72-dedicated-sql-pools"></a>步驟7.2：專用的 SQL 集區

若要授與 **單一** 專用 SQL 集區的存取權，請在 Synapse SQL 腳本編輯器中遵循下列步驟：

1. 在目標資料庫上執行下列命令，以在資料庫中建立使用者，方法是使用 [ *連接到* ] 下拉式清單來選取：

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
> 如果不想要授與 *db_owner* 許可權， *db_datareader* 和 *db_datawriter* 可以處理讀取/寫入權限。
> 若要讓 Spark 使用者直接從 Spark 讀取和寫入 SQL 集區，則需要 *db_owner* 許可權。

建立使用者之後，請驗證無伺服器 SQL 集區可以查詢儲存體帳戶。

### <a name="step-73-sl-access-control-for-workspace-pipeline-runs"></a>步驟7.3：工作區管線執行的 SL 存取控制

### <a name="workspace-managed-identity"></a>工作區受控識別

> [!IMPORTANT]
> 若要成功執行包含參考 SQL 集區之資料集或活動的管線，必須將 SQL 集區的存取權授與工作區身分識別。

在每個 SQL 集區上執行下列命令，允許工作區受控識別可在 SQL 集區資料庫上執行管線：

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

## <a name="step-8-add-users-to-security-groups"></a>步驟8：將使用者新增至安全性群組

您的存取控制系統初始設定已完成。

若要管理存取權，您可以新增和移除已設定之安全性群組的使用者。  雖然您可以手動將使用者指派給 Synapse 角色，但這樣做並不會一致地設定其許可權。 因此，請只在安全性群組中新增或移除使用者。

## <a name="step-9-network-security"></a>步驟9：網路安全性

為保護工作區的最後一個步驟，您應該使用下列方式保護網路存取：
- [工作區防火牆](./synapse-workspace-ip-firewall.md)
- [受控虛擬網路](./synapse-workspace-managed-vnet.md) 
- [私人端點](./synapse-workspace-managed-private-endpoints.md)
- [私人連結](../../azure-sql/database/private-endpoint-overview.md)

## <a name="step-10-completion"></a>步驟10：完成

您的工作區現在已完整設定並受到保護。

## <a name="supporting-more-advanced-scenarios"></a>支援更先進的案例

本指南著重于設定基本存取控制系統。 您可以藉由建立額外的安全性群組，並在更明確的範圍指派這些群組更細微的角色，來支援更高階的案例。 請考慮下列情況：

針對工作區 **啟用 Git 支援**，以取得更先進的開發案例，包括 CI/CD。  在 Git 模式中，Git 許可權會決定使用者是否可以認可其工作分支的變更。  發行至服務的作業只會從共同作業分支進行。  請考慮為需要在工作分支中開發和偵錯工具的開發人員建立安全性群組，但不需要將變更發佈到即時服務。

**限制開發人員存取** 特定資源。  針對只需要存取特定資源的開發人員，建立更精細的安全性群組。  將設定為特定 Spark 集區、整合執行時間或認證範圍的適當 Synapse 角色指派給這些群組。

**限制操作員存取程式碼** 成品。  針對需要監視 Synapse 計算資源和查看記錄，但不需要存取程式碼或將更新發行至服務之作業狀態的操作員，建立安全性群組。 將計算操作員角色的範圍指派給這些群組，以指定 Spark 集區和整合執行時間。  

## <a name="next-steps"></a>後續步驟

瞭解 [如何管理 SYNAPSE RBAC 角色指派](./how-to-manage-synapse-rbac-role-assignments.md) 建立 [Synapse 工作區](../quickstart-create-workspace.md)

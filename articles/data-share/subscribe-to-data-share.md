---
title: 教學課程：接受和接收資料 - Azure Data Share
description: 教學課程 - 使用 Azure Data Share 接受和接收資料
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 11/12/2020
ms.openlocfilehash: a225989f0670e9b62b00a35bac719c9357c8a130
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659602"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>教學課程：使用 Azure Data Share 接受和接收資料  

在此教學課程中，您將了解如何使用 Azure Data Share 接受資料共用邀請。 您會了解如何接收您所能共用的資料，以及如何啟用定期重新整理間隔以確保您永遠會有所能共用資料的最新快照集。 

> [!div class="checklist"]
> * 如何接受 Azure Data Share 邀請
> * 建立 Azure Data Share 帳戶
> * 指定資料的目的地
> * 建立資料共用訂閱以獲得排定的重新整理

## <a name="prerequisites"></a>Prerequisites
您必須先佈建一些 Azure 資源 (下面會列出) 才可以接受資料共用邀請。 

請先確定您已完成所有必要條件再接受資料共用邀請。 

* Azure 訂用帳戶：如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
* 資料共用邀請：Microsoft Azure 所送來的邀請，主旨標題為「來自 **<yourdataprovider@domain.com>** 的 Azure Data Share 邀請」。
* 在您將建立資料共用資源的 Azure 訂用帳戶及目標 Azure 資料存放區所在的 Azure 訂用帳戶中，註冊 [Microsoft.DataShare 資源提供者](concepts-roles-permissions.md#resource-provider-registration)。

### <a name="receive-data-into-a-storage-account"></a>將資料接收到儲存體帳戶中

* Azure 儲存體帳戶：如果您還沒有此帳戶，則可以建立 [Azure 儲存體帳戶](../storage/common/storage-account-create.md)。 
* 寫入儲存體帳戶的權限，存在於 Microsoft.Storage/storageAccounts/write  中。 此權限存在於參與者角色中。 
* 將角色指派新增至儲存體帳戶的權限，存在於 Microsoft.Authorization/role assignments/write  中。 此權限存在於擁有者角色中。  

### <a name="receive-data-into-a-sql-based-target"></a>將資料接收到 SQL 型目標
如果您選擇將資料接收到 Azure SQL Database 或 Azure Synapse Analytics，以下是必要條件清單。 

#### <a name="prerequisites-for-receiving-data-into-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>將資料接收到 Azure SQL Database 或 Azure Synapse Analytics (先前稱為 Azure SQL DW) 的必要條件
您可以遵循[逐步解說示範](https://youtu.be/aeGISgK1xro)來設定必要條件。

* Azure SQL Database 或 Azure Synapse Analytics (先前稱為 Azure SQL DW)。
* 對 SQL Server 上的資料庫進行寫入的權限，存在於 Microsoft.Sql/servers/databases/write  中。 此權限存在於 **參與者** 角色中。 
* Data Share 資源的受控識別用來存取 Azure SQL Database 或 Azure Synapse Analytics 的權限。 這可以透過下列步驟完成： 
    1. 在 Azure 入口網站中瀏覽至 SQL 伺服器，並將您自己設定為 **Azure Active Directory 管理員**。
    1. 使用[查詢編輯器](../azure-sql/database/connect-query-portal.md#connect-using-azure-active-directory)或 SQL Server Management Studio 搭配 Azure Active Directory 驗證，連線到 Azure SQL Database/資料倉儲。 
    1. 執行下列指令碼，將 Data Share 受控識別新增為 'db_datareader, db_datawriter, db_ddladmin'。 您必須使用 Active Directory 連線，而不是使用 SQL Server 驗證。 

        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```      
        請注意， *<share_acc_name>* 是您 Data Share 資源的名稱。 如果您尚未建立 Data Share 資源，您可以稍後再回到此先決條件。         

* SQL Server 防火牆存取。 這可以透過下列步驟完成： 
    1. 在 Azure 入口網站的 SQL Server 中，瀏覽至 [防火牆和虛擬網路] 
    1. 在 [允許 Azure 服務和資源存取此伺服器] 上按一下 [是]。
    1. 按一下 [+ 新增用戶端 IP]。 用戶端 IP 位址可能會有所變更。 下次從 Azure 入口網站共用 SQL 資料時，可能需要重複執行此程序。 您也可以新增 IP 範圍。
    1. 按一下 [檔案] 。 
 
#### <a name="prerequisites-for-receiving-data-into-azure-synapse-analytics-workspace-sql-pool"></a>將資料接收到 Azure Synapse Analytics (工作區) SQL 集區的必要條件

* Azure Synapse Analytics (工作區) 專用的 SQL 集區。 目前不支援將資料接收到無伺服器 SQL 集區。
* 寫入 Synapse 工作區中 SQL 集區的權限 (存在於 Microsoft.Synapse/workspaces/sqlPools/write)。 此權限存在於 **參與者** 角色中。
* Data Share 資源的受控識別權限用來存取 Synapse 工作區 SQL 集區的權限。 這可以透過下列步驟完成： 
    1. 在 Azure 入口網站中，瀏覽至 Synapse 工作區。 從左側導覽中選取 SQL Active Directory 管理員，並將您自己設定為 **Azure Active Directory 管理員**。
    1. 開啟 Synapse Studio，從左側導覽中選取 [管理]。 選取 [安全性] 底下的 [存取控制]。 將您自己指派為 [SQL 管理員] 或 [工作區管理員] 角色。
    1. 在 Synapse Studio 中，從左側導覽中選取 [開發]。 在 SQL 集區中執行下列指令碼，將 Data Share 資源受控識別新增為'db_datareader, db_datawriter, db_ddladmin'。 
    
        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```                   
       請注意， *<share_acc_name>* 是您 Data Share 資源的名稱。 如果您尚未建立 Data Share 資源，您可以稍後再回到此先決條件。  

* Synapse 工作區防火牆存取。 這可以透過下列步驟完成： 
    1. 在 Azure 入口網站中，瀏覽至 Synapse 工作區。 從左側導覽中選取 [防火牆]。
    1. 在 [允許 Azure 服務與資源能存取此工作區] 上，按一下 [開啟]。
    1. 按一下 [+ 新增用戶端 IP]。 用戶端 IP 位址可能會有所變更。 下次從 Azure 入口網站共用 SQL 資料時，可能需要重複執行此程序。 您也可以新增 IP 範圍。
    1. 按一下 [檔案] 。 

### <a name="receive-data-into-an-azure-data-explorer-cluster"></a>將資料接收到 Azure 資料總管叢集中： 

* 與資料提供者的資料總管叢集位於相同 Azure 資料中心的 Azure 資料總管叢集：如果您還沒有此叢集，可以建立 [Azure 資料總管叢集](/azure/data-explorer/create-cluster-database-portal)。 如果您不知道資料提供者叢集的 Azure 資料中心，您可以在稍後的程序中建立叢集。
* 寫入 Azure 資料總管叢集的權限，存在於 Microsoft.Kusto/clusters/write  中。 此權限存在於參與者角色中。 
* 將角色指派新增至 Azure 資料總管叢集的權限，存在於 Microsoft.Authorization/role assignments/write  中。 此權限存在於擁有者角色中。 

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="open-invitation"></a>開啟邀請

### <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 您可以從電子郵件或直接從 Azure 入口網站開啟邀請。 

   若要從電子郵件開啟邀請，請檢查您的收件匣是否有來自您資料提供者的邀請。 邀請會來自 Microsoft Azure，標題為 **來自 <yourdataprovider@domain.com> 的 Azure Data Share 邀請**。 按一下 [檢視邀請]  以查看您在 Azure 中的邀請。 

   若要直接從 Azure 入口網站開啟邀請，請在 Azure 入口網站中搜尋 **Data Share 邀請**。 此動作會帶您前往 Data Share 邀請的清單。

   ![邀請清單](./media/invitations.png "邀請清單") 

1. 選取您想要檢視的共用。 

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

準備您的 Azure CLI 環境，然後檢視您的邀請。

從準備適用於 Azure CLI 的環境開始：

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

執行 [az datashare consumer invitation list](/cli/azure/ext/datashare/datashare/consumer/invitation#ext_datashare_az_datashare_consumer_invitation_list) 命令來查看您目前的邀請：

```azurecli
az datashare consumer invitation list --subscription 11111111-1111-1111-1111-111111111111
```

複製您的邀請識別碼，以便在下一節中使用。

---

## <a name="accept-invitation"></a>接受邀請

### <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 請確定您已檢閱過所有欄位，包括 [使用規定]  。 如果您同意使用規定，則必須核取該方塊以表示您同意。 

   ![使用規定](./media/terms-of-use.png "使用規定") 

1. 在 [目標 Data Share 帳戶]  底下，選取要作為 Data Share 部署目的地的訂用帳戶和資源群組。 

   針對 [Data Share 帳戶]  欄位，如果您沒有現有的 Data Share 帳戶，請選取 [新建]  。 否則，請選取您想要用來接受資料共用的現有 Data Share 帳戶。 

   針對 [已收到的共用名稱]  欄位，您可以保留資料提供者所指定的預設值，也可以為已接收的共用指定新名稱。 

   同意使用規定並指定了管理收到之共用的資料共用帳戶後，請選取 [接受並設定]。 此時會建立共用訂用帳戶。 

   ![接受選項](./media/accept-options.png "接受選項") 

   此動作會帶您前往 Data Share 帳戶中所接收的共用。 

   如果您不想要接受邀請，請選取 [拒絕]。 

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 [az datashare consumer share-subscription create](/cli/azure/ext/datashare/datashare/consumer/share-subscription#ext_datashare_az_datashare_consumer_share_subscription_create) 命令來建立 Data Share。

```azurecli
az datashare consumer share-subscription create --resource-group share-rg \
  --name "Fabrikam Solutions" --account-name FabrikamDataShareAccount \
  --invitation-id 89abcdef-0123-4567-89ab-cdef01234567 \
  --source-share-location "East US 2" --subscription 11111111-1111-1111-1111-111111111111
```

---

## <a name="configure-received-share"></a>設定已接收的共用

### <a name="portal"></a>[入口網站](#tab/azure-portal)

請遵循下列步驟來設定要接收資料的位置。

1. 選取 **資料集** 索引標籤。勾選要指派目的地之資料集旁的方塊。 選取 [+ 對應至目標] 以選擇目標資料存放區。 

   ![對應到目標](./media/dataset-map-target.png "對應到目標") 

1. 選取您想要存放資料的目標資料存放區類型。 系統將會覆寫目標資料存放區中，具有相同路徑和名稱的任何資料檔案或資料表。 

   針對就地共用，請選取指定位置中的資料存放區。 位置是資料提供者來源資料存放區所在的 Azure 資料中心。 對應資料集之後，您可以遵循目標路徑中的連結來存取資料。

   ![目標儲存體帳戶](./media/dataset-map-target-sql.png "目標儲存體") 

1. 對於以快照集為基礎的共用，如果資料提供者已建立快照集排程來定期更新資料，也可以選取 [快照集排程] 索引標籤來啟用快照集排程。勾選快照集排程旁的方塊，然後選取 [+ 啟用]。

   ![啟用快照集排程](./media/enable-snapshot-schedule.png "啟用快照集排程")

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用這些命令來設定您想要接收資料的位置。

1. 執行 [az datashare consumer share-subscription list-source-dataset](/cli/azure/ext/datashare/datashare/consumer/share-subscription#ext_datashare_az_datashare_consumer_share_subscription_list_source_dataset) 命令來取得資料集識別碼：

   ```azurecli
   az datashare consumer share-subscription list-source-dataset \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "[0].dataSetId"
   ```

1. 執行 [az storage account create](/cli/azure/storage/account#az_storage_account_create) 命令，為此 Data Share 建立儲存體帳戶：

   ```azurecli
   az storage account create --resource-group "share-rg" --name "FabrikamDataShareAccount" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. 使用 [az storage account show](/cli/azure/storage/account#az_storage_account_show) 命令來取得儲存體帳戶識別碼：

   ```azurecli
   az storage account show --resource-group "share-rg" --name "FabrikamDataShareAccount" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "id"
   ```

1. 使用下列命令來取得帳戶主體識別碼：

   ```azurecli
   az datashare account show --resource-group "share-rg" --name "cli_test_consumer_account" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "identity.principalId"
   ```

1. 使用 [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) 命令來建立帳戶主體的角色指派：

   ```azurecli
   az role assignment create --role "01234567-89ab-cdef-0123-456789abcdef" \
     --assignee-object-id 6789abcd-ef01-2345-6789-abcdef012345 
     --assignee-principal-type ServicePrincipal --scope 456789ab-cdef-0123-4567-89abcdef0123 \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. 根據資料集識別碼建立對應的變數：

   ```azurecli
   $mapping='{\"data_set_id\":\"' + $dataset_id + '\",\"container_name\":\"newcontainer\",
     \"storage_account_name\":\"datashareconsumersa\",\"kind\":\"BlobFolder\",\"prefix\":\"consumer\"}'
   ```

1. 使用 [az datashare consumer dataset-mapping create](/cli/azure/ext/datashare/datashare/consumer/dataset-mapping#ext_datashare_az_datashare_consumer_dataset_mapping_create) 命令來建立資料集對應：

   ```azurecli
   az datashare consumer dataset-mapping create --resource-group "share-rg" \
     --name "consumer-data-set-mapping" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" --mapping $mapping \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. 執行 [az datashare consumer share-subscription synchronization start](/cli/azure/ext/datashare/datashare/consumer/share-subscription/synchronization#ext_datashare_az_datashare_consumer_share_subscription_synchronization_start) 命令來啟動資料集同步。

   ```azurecli
   az datashare consumer share-subscription synchronization start \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount"  \
     --share-subscription-name "Fabrikam Solutions" --synchronization-mode "Incremental" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

   執行 [az datashare consumer share-subscription synchronization list](/cli/azure/ext/datashare/datashare/consumer/share-subscription/synchronization#ext_datashare_az_datashare_consumer_share_subscription_synchronization_list) 命令來查看您的同步清單：

   ```azurecli
   az datashare consumer share-subscription synchronization list \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

   使用 [az datashare consumer share-subscription list-source-share-synchronization-setting](/cli/azure/ext/datashare/datashare/consumer/share-subscription#ext_datashare_az_datashare_consumer_share_subscription_list_source_share_synchronization_setting) 命令來查看您共用上設定的同步設定。

   ```azurecli
   az datashare consumer share-subscription list-source-share-synchronization-setting \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" --subscription 11111111-1111-1111-1111-111111111111
   ```

---

## <a name="trigger-a-snapshot"></a>觸發快照集

### <a name="portal"></a>[入口網站](#tab/azure-portal)

這些步驟僅適用於以快照集為基礎的共用。

1. 您可以選取 [觸發程序快照集]及 [詳細資料] 索引標籤以觸發快照集。 在這裡，您可以觸發資料的完整或累加快照集。 如果您是第一次接收來自資料提供者的資料，請選取 [完整複本]。 

   ![觸發快照集](./media/trigger-snapshot.png "觸發快照集") 

1. 當最後一次執行的狀態為「成功」時，請移至目標資料存放區檢視已接收的資料。 選取 [資料集]，然後按一下 [目標路徑] 中的連結。 

   ![取用者資料集](./media/consumer-datasets.png "取用者資料集對應") 

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

執行 [az datashare consumer trigger create](/cli/azure/ext/datashare/datashare/consumer/trigger#ext_datashare_az_datashare_consumer_trigger_create) 命令來觸發快照集：

```azurecli
az datashare consumer trigger create --resource-group "share-rg" \
  --name "share_test_trigger" --account-name "FabrikamDataShareAccount" \
  --share-subscription-name "Fabrikam Solutions" --recurrence-interval "Day" \
  --synchronization-time "2020-04-23 18:00:00 +00:00" --kind ScheduleBased \
  --subscription 11111111-1111-1111-1111-111111111111
```

> [!NOTE]
> 此命令只適用於快照集形式的共用。

---

## <a name="view-history"></a>檢視歷程記錄
此步驟僅適用於以快照集為基礎的共用。 若要檢視快照集的記錄，請選取 [歷程記錄] 索引標籤。您會在這裡找到過去 30 天所有產生的快照集歷程記錄。

## <a name="clean-up-resources"></a>清除資源

當不再需要資源時，請移至 [Data Share 概觀] 頁面，然後選取 [刪除] 來移除資源。

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已了解如何接受和接收 Azure Data Share。 若要深入了解 Azure Data Share 概念，請繼續閱讀 Azure Data Share 詞彙。

> [!div class="nextstepaction"]
> [Azure Data Share 概念](terminology.md)
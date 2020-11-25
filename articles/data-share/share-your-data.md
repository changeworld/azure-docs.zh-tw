---
title: 教學課程：在貴組織外部共用 - Azure Data Share
description: 教學課程 - 使用 Azure Data Share 與客戶及合作夥伴共用資料
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 11/12/2020
ms.openlocfilehash: 89c2a725b853b5a2a7578dccc1fd503917e12962
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659619"
---
# <a name="tutorial-share-data-using-azure-data-share"></a>教學課程：使用 Azure Data Share 共用資料  

在本教學課程中，您會了解如何設定新的 Azure Data Share，並開始與 Azure 組織外的客戶及合作夥伴共用資料。 

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 建立 Data Share。
> * 將資料集新增至 Data Share。
> * 為 Data Share 啟用快照集排程。 
> * 將收件者新增至 Data Share。 

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶：如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
* 您收件者的 Azure 登入電子郵件地址 (不能使用其電子郵件別名)。
* 如果來源 Azure 資料存放區位於與您將用來建立資料共用資源的不同 Azure 訂用帳戶中，請在 Azure 資料存放區所在的訂用帳戶中註冊 [Microsoft.DataShare 資源提供者](concepts-roles-permissions.md#resource-provider-registration)。 

### <a name="share-from-a-storage-account"></a>從儲存體帳戶共用

* Azure 儲存體帳戶：如果您還沒有此帳戶，則可以建立 [Azure 儲存體帳戶](../storage/common/storage-account-create.md)
* 寫入儲存體帳戶的權限，存在於 Microsoft.Storage/storageAccounts/write  中。 此權限存在於 **參與者** 角色中。
* 將角色指派新增至儲存體帳戶的權限，存在於 Microsoft.Authorization/role assignments/write  中。 此權限存在於 **擁有者** 角色中。 


### <a name="share-from-a-sql-based-source"></a>從 SQL 型來源共用
以下是從 SQL 來源共用資料的必要條件清單。 

#### <a name="prerequisites-for-sharing-from-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>從 Azure SQL Database 或 Azure Synapse Analytics (先前稱為 Azure SQL DW) 共用的必要條件
您可以遵循[逐步解說示範](https://youtu.be/hIE-TjJD8Dc)來設定必要條件。

* Azure SQL Database 或 Azure Synapse Analytics (先前稱為 Azure SQL DW)，包含您要共用的資料表和檢視。
* 對 SQL Server 上的資料庫進行寫入的權限，存在於 Microsoft.Sql/servers/databases/write  中。 此權限存在於 **參與者** 角色中。
* Data Share 資源的受控識別用來存取資料庫的權限。 這可以透過下列步驟完成： 
    1. 在 Azure 入口網站中瀏覽至 SQL 伺服器，並將您自己設定為 **Azure Active Directory 管理員**。
    1. 使用[查詢編輯器](../azure-sql/database/connect-query-portal.md#connect-using-azure-active-directory)或 SQL Server Management Studio 搭配 Azure Active Directory 驗證，連線到 Azure SQL Database/資料倉儲。 
    1. 執行下列指令碼，將 Data Share 資源受控識別新增為 db_datareader。 您必須使用 Active Directory 連線，而不是使用 SQL Server 驗證。 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       請注意， *<share_acc_name>* 是您 Data Share 資源的名稱。 如果您尚未建立 Data Share 資源，您可以稍後再回到此先決條件。  

* 具有 **'db_datareader'** 存取權的 Azure SQL Database 使用者，可瀏覽並選取您想共用的資料表和/或檢視。 

* SQL Server 防火牆存取。 這可以透過下列步驟完成： 
    1. 在 Azure 入口網站中，瀏覽到 SQL 伺服器。 在左側導覽中選取 [防火牆與虛擬網路]。
    1. 在 [允許 Azure 服務和資源存取此伺服器] 上按一下 [是]。
    1. 按一下 [+ 新增用戶端 IP]。 用戶端 IP 位址可能會有所變更。 下次從 Azure 入口網站共用 SQL 資料時，可能需要重複執行此程序。 您也可以新增 IP 範圍。
    1. 按一下 [檔案] 。 

#### <a name="prerequisites-for-sharing-from-azure-synapse-analytics-workspace-sql-pool"></a>從 Azure Synapse Analytics (工作區) SQL 集區共用的必要條件

* * Azure Synapse Analytics (工作區) 專用的 SQL 集區及您要共用的資料表。 目前不支援共用檢視。 目前不支援從無伺服器 SQL 集區共用。
* 寫入 Synapse 工作區中 SQL 集區的權限 (存在於 Microsoft.Synapse/workspaces/sqlPools/write)。 此權限存在於 **參與者** 角色中。
* Data Share 資源的受控識別權限用來存取 Synapse 工作區 SQL 集區的權限。 這可以透過下列步驟完成： 
    1. 在 Azure 入口網站中，瀏覽至 Synapse 工作區。 從左側導覽中選取 SQL Active Directory 管理員，並將您自己設定為 **Azure Active Directory 管理員**。
    1. 開啟 Synapse Studio，從左側導覽中選取 [管理]。 選取 [安全性] 底下的 [存取控制]。 將您自己指派為 [SQL 管理員] 或 [工作區管理員] 角色。
    1. 在 Synapse Studio 中，從左側導覽中選取 [開發]。 在 SQL 集區中執行下列指令碼，將 Data Share 資源受控識別新增為 db_datareader。 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       請注意， *<share_acc_name>* 是您 Data Share 資源的名稱。 如果您尚未建立 Data Share 資源，您可以稍後再回到此先決條件。  

* Synapse 工作區防火牆存取。 這可以透過下列步驟完成： 
    1. 在 Azure 入口網站中，瀏覽至 Synapse 工作區。 從左側導覽中選取 [防火牆]。
    1. 在 [允許 Azure 服務與資源能存取此工作區] 上，按一下 [開啟]。
    1. 按一下 [+ 新增用戶端 IP]。 用戶端 IP 位址可能會有所變更。 下次從 Azure 入口網站共用 SQL 資料時，可能需要重複執行此程序。 您也可以新增 IP 範圍。
    1. 按一下 [檔案] 。 


### <a name="share-from-azure-data-explorer"></a>從 Azure 資料總管共用
* Azure 資料總管叢集，其中包含您想要共用的資料庫。
* 寫入 Azure 資料總管叢集的權限，存在於 Microsoft.Kusto/clusters/write  中。 此權限存在於 **參與者** 角色中。
* 將角色指派新增至 Azure 資料總管叢集的權限，存在於 Microsoft.Authorization/role assignments/write  中。 此權限存在於 **擁有者** 角色中。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="create-a-data-share-account"></a>建立 Data Share 帳戶

### <a name="portal"></a>[入口網站](#tab/azure-portal)

在 Azure 資源群組中建立 Azure Data Share 資源。

1. 選取入口網站左上角的功能表按鈕，再選取 [建立資源] (+)。

1. 搜尋「Data Share」  。

1. 選取 [Data Share]，然後選取 [建立]  。

1. 使用下列資訊填寫 Azure Data Share 資源的基本詳細資料。 

     **設定** | **建議的值** | **欄位描述**
    |---|---|---|
    | 訂用帳戶 | 您的訂用帳戶 | 選取您要用於資料共用帳戶的 Azure 訂用帳戶。|
    | 資源群組 | *testresourcegroup* | 使用現有資源群組，或建立新的資源群組。 |
    | Location | *美國東部 2* | 選取資料共用帳戶的區域。
    | 名稱 | *datashareaccount* | 指定資料共用帳戶的名稱。 |
    | | |

1. 選取 [檢閱 + 建立]，然後選取 [建立] 來佈建您的資料共用帳戶。 佈建新的資料共用帳戶一般需要大約不到 2 分鐘的時間。 

1. 部署完成後，請選取 [移至資源]  。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

在 Azure 資源群組中建立 Azure Data Share 資源。

從準備適用於 Azure CLI 的環境開始：

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

使用下列命令來建立資源：

1. 使用 [az account set](/cli/azure/account#az_account_set) 命令，將您的訂用帳戶設定為目前的預設訂用帳戶：

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. 執行 [az provider register](/cli/azure/provider#az_provider_register) 命令以註冊資源提供者：

   ```azurecli
   az provider register --name "Microsoft.DataShare"
   ```

1. 執行 [az group create](/cli/azure/group#az_group_create) 命令建立資源群組，或使用現有的資源群組：

   ```azurecli
   az group create --name testresourcegroup --location "East US 2"
   ```

1. 執行 [az datashare account create](/cli/azure/ext/datashare/datashare/account#ext_datashare_az_datashare_account_create) 命令來建立 Data Share 帳戶：

   ```azurecli
   az datashare account create --resource-group testresourcegroup --name datashareaccount --location "East US 2" 
   ```

   執行 [az datashare account list](/cli/azure/ext/datashare/datashare/account#ext_datashare_az_datashare_account_list) 命令來查看您的 Data Share 帳戶：

   ```azurecli
   az datashare account list --resource-group testresourcegroup
   ```

---

## <a name="create-a-share"></a>建立共用

### <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 瀏覽至 [Data Share 概觀] 頁面。

    ![共用您的資料](./media/share-receive-data.png "共用您的資料") 

1. 選取 [開始共用資料]。

1. 選取 [建立]  。   

1. 填寫共用的詳細資料。 指定名稱、共用類型、共用內容的說明和使用規定 (選擇性)。 

    ![EnterShareDetails](./media/enter-share-details.png "輸入共用詳細資料") 

1. 選取 [繼續]。

1. 若要將資料集新增至共用，請選取 [新增資料集]。 

    ![將資料集新增至共用](./media/datasets.png "資料集")

1. 選取您想要新增的資料集類型。 根據您在先前的步驟中選取的共用類型 (快照集或就地)，您將看到不同的資料集類型清單。 如果從 Azure SQL Database 或 Azure Synapse Analytics (先前稱為 Azure SQL DW) 共用，系統會提示您提供 SQL 認證來列出資料表。

    ![AddDatasets](./media/add-datasets.png "新增資料集")    

1. 瀏覽至您想要共用的物件，然後選取 [新增資料集]。 

    ![SelectDatasets](./media/select-datasets.png "選取資料集")    

1. 在 [收件者] 索引標籤中，藉由選取 [+ 新增收件者] 來輸入資料取用者的電子郵件地址。 

    ![AddRecipients](./media/add-recipient.png "新增收件者") 

1. 選取 [繼續]。

1. 如果您已選取快照集共用類型，則可以設定快照集排程，將資料的更新提供給資料取用者。 

    ![EnableSnapshots](./media/enable-snapshots.png "啟用快照集") 

1. 選取開始時間和週期間隔。 

1. 選取 [繼續]。

1. 在 [檢閱 + 建立] 索引標籤中，檢閱 [套件內容]、[設定]、[收件者] 及 [同步處理設定]。 選取 [建立]  。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 執行 [az storage account create](/cli/azure/storage/account#az_storage_account_create) 命令來建立 Data Share：

   ```azurecli
   az storage account create --resource-group testresourcegroup --name ContosoMarketplaceAccount
   ```

1. 使用 [az storage container create](/cli/azure/storage/container#az_storage_container_create) 命令，為上一個命令中的共用建立容器：

   ```azurecli
   az storage container create --name ContosoMarketplaceContainer --account-name ContosoMarketplaceAccount
   ```

1. 執行 [az datashare create](/cli/azure/ext/datashare/datashare#ext_datashare_az_datashare_create) 命令來建立您的 Data Share：

   ```azurecli
   az datashare create --resource-group testresourcegroup \
     --name ContosoMarketplaceDataShare --account-name ContosoMarketplaceAccount \
     --description "Data Share" --share-kind "CopyBased" --terms "Confidential"
   ```

1. 使用 [az datashare invitation create](/cli/azure/ext/datashare/datashare/invitation#ext_datashare_az_datashare_invitation_create) 命令，為指定的位址建立邀請：

   ```azurecli
   az datashare invitation create --resource-group testresourcegroup \
     --name DataShareInvite --share-name ContosoMarketplaceDataShare \
     --account-name ContosoMarketplaceAccount --target-email "jacob@fabrikam"
   ```

---

Azure Data Share 現已建立完成，而且 Data Share 的收件者現已準備好接受邀請。

## <a name="clean-up-resources"></a>清除資源

當不再需要資源時，請移至 [Data Share 概觀] 頁面，然後選取 [刪除] 來移除資源。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何建立 Azure Data Share 並邀請收件者。 若要了解資料取用者要如何接受和接收資料共用，請繼續閱讀接受和接收資料教學課程。

> [!div class="nextstepaction"]
> [教學課程：使用 Azure Data Share 接受和接收資料](subscribe-to-data-share.md)

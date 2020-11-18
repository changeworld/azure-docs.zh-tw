---
title: 教學課程：接受和接收資料 - Azure Data Share
description: 教學課程 - 使用 Azure Data Share 接受和接收資料
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 08/14/2020
ms.openlocfilehash: 9031ea2d862a23df5d597b790fffc49e624e53fb
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491914"
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

### <a name="receive-data-into-a-storage-account"></a>將資料接收到儲存體帳戶中： 

* Azure 儲存體帳戶：如果您還沒有此帳戶，則可以建立 [Azure 儲存體帳戶](../storage/common/storage-account-create.md)。 
* 寫入儲存體帳戶的權限，存在於 Microsoft.Storage/storageAccounts/write  中。 此權限存在於參與者角色中。 
* 將角色指派新增至儲存體帳戶的權限，存在於 Microsoft.Authorization/role assignments/write  中。 此權限存在於擁有者角色中。  

### <a name="receive-data-into-a-sql-based-target"></a>將資料接收到 SQL 型目標：

* 對 SQL Server 上的資料庫進行寫入的權限，存在於 Microsoft.Sql/servers/databases/write  中。 此權限存在於參與者角色中。 
* 資料共用資源的受控識別用來存取 Azure SQL Database 或 Azure Synapse Analytics 的權限。 這可以透過下列步驟完成： 
    1. 將您自己設定為 SQL Server 的 Azure Active Directory 系統管理員。
    1. 使用 Azure Active Directory 連線到 Azure SQL Database/資料倉儲。
    1. 使用查詢編輯器 (預覽) 執行下列指令碼，將 Data Share 受控識別新增為 'db_datareader, db_datawriter, db_ddladmin'。 您必須使用 Active Directory 連線，而不是使用 SQL Server 驗證。 

        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```      
        請注意， *<share_acc_name>* 是您 Data Share 資源的名稱。 如果您尚未建立 Data Share 資源，您可以稍後再回到此先決條件。         

* 用戶端 IP SQL Server 防火牆存取。 這可以透過下列步驟完成： 
    1. 在 Azure 入口網站的 SQL Server 中，瀏覽至 [防火牆和虛擬網路] 
    1. 按一下 [開啟]  切換開關，以允許存取 Azure 服務。
    1. 按一下 [+ 新增用戶端 IP]  ，然後按一下 [儲存]  。 用戶端 IP 位址可能會有所變更。 下次從 Azure 入口網站將資料接收到 SQL 目標時，可能需要重複執行此程序。 您也可以新增 IP 範圍。 


### <a name="receive-data-into-an-azure-data-explorer-cluster"></a>將資料接收到 Azure 資料總管叢集中： 

* 與資料提供者的資料總管叢集位於相同 Azure 資料中心的 Azure 資料總管叢集：如果您還沒有此叢集，可以建立 [Azure 資料總管叢集](/azure/data-explorer/create-cluster-database-portal)。 如果您不知道資料提供者叢集的 Azure 資料中心，您可以在稍後的程序中建立叢集。
* 寫入 Azure 資料總管叢集的權限，存在於 Microsoft.Kusto/clusters/write  中。 此權限存在於參與者角色中。 
* 將角色指派新增至 Azure 資料總管叢集的權限，存在於 Microsoft.Authorization/role assignments/write  中。 此權限存在於擁有者角色中。 

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="open-invitation"></a>開啟邀請

1. 您可以從電子郵件或直接從 Azure 入口網站開啟邀請。 

   若要從電子郵件開啟邀請，請檢查您的收件匣是否有來自您資料提供者的邀請。 邀請會來自 Microsoft Azure，標題為 **來自 <yourdataprovider@domain.com> 的 Azure Data Share 邀請**。 按一下 [檢視邀請]  以查看您在 Azure 中的邀請。 

   若要直接從 Azure 入口網站開啟邀請，請在 Azure 入口網站中搜尋 **Data Share 邀請**。 這會帶您前往 Data Share 邀請的清單。

   ![邀請清單](./media/invitations.png "邀請清單") 

1. 選取您想要檢視的共用。 

## <a name="accept-invitation"></a>接受邀請
1. 請確定您已檢閱過所有欄位，包括 [使用規定]  。 如果您同意使用規定，則必須核取該方塊以表示您同意。 

   ![使用規定](./media/terms-of-use.png "使用規定") 

1. 在 [目標 Data Share 帳戶]  底下，選取要作為 Data Share 部署目的地的訂用帳戶和資源群組。 

   針對 [Data Share 帳戶]  欄位，如果您沒有現有的 Data Share 帳戶，請選取 [新建]  。 否則，請選取您想要用來接受資料共用的現有 Data Share 帳戶。 

   針對 [已收到的共用名稱]  欄位，您可以保留資料提供者所指定的預設值，也可以為已接收的共用指定新名稱。 

   同意使用規定並指定了管理收到之共用的資料共用帳戶後，請選取 [接受並設定]。 此時會建立共用訂用帳戶。 

   ![接受選項](./media/accept-options.png "接受選項") 

   同時會帶您前往資料共用帳戶中所接收的共用。 

   如果您不想要接受邀請，請選取 [拒絕]。 

## <a name="configure-received-share"></a>設定已接收的共用
請遵循下列步驟來設定要接收資料的位置。

1. 選取 **資料集** 索引標籤。勾選要指派目的地之資料集旁的方塊。 選取 [+ 對應至目標] 以選擇目標資料存放區。 

   ![對應到目標](./media/dataset-map-target.png "對應到目標") 

1. 選取您想要存放資料的目標資料存放區類型。 系統將會覆寫目標資料存放區中，具有相同路徑和名稱的任何資料檔案或資料表。 

   針對就地共用，請選取指定位置中的資料存放區。 位置是資料提供者來源資料存放區所在的 Azure 資料中心。 對應資料集之後，您可以遵循目標路徑中的連結來存取資料。

   ![目標儲存體帳戶](./media/dataset-map-target-sql.png "目標儲存體") 

1. 對於以快照集為基礎的共用，如果資料提供者已建立快照集排程來定期更新資料，也可以選取 [快照集排程] 索引標籤來啟用快照集排程。勾選快照集排程旁的方塊，然後選取 [+ 啟用]。

   ![啟用快照集排程](./media/enable-snapshot-schedule.png "啟用快照集排程")

## <a name="trigger-a-snapshot"></a>觸發快照集
這些步驟僅適用於以快照集為基礎的共用。

1. 您可以選取 [觸發程序快照集]及 [詳細資料] 索引標籤以觸發快照集。 在這裡，您可以觸發資料的完整或累加快照集。 如果您是第一次接收來自資料提供者的資料，請選取 [完整複本]。 

   ![觸發快照集](./media/trigger-snapshot.png "觸發快照集") 

1. 當最後一次執行的狀態為「成功」時，請移至目標資料存放區檢視已接收的資料。 選取 [資料集]，然後按一下 [目標路徑] 中的連結。 

   ![取用者資料集](./media/consumer-datasets.png "取用者資料集對應") 

## <a name="view-history"></a>檢視歷程記錄
此步驟僅適用於以快照集為基礎的共用。 若要檢視快照集的記錄，請選取 [歷程記錄] 索引標籤。您會在這裡找到過去 30 天所有產生的快照集歷程記錄。

## <a name="clean-up-resources"></a>清除資源

當不再需要資源時，請移至 [Data Share 概觀] 頁面，然後選取 [刪除] 來移除資源。

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已了解如何接受和接收 Azure Data Share。 若要深入了解 Azure Data Share 概念，請繼續閱讀 Azure Data Share 詞彙。

> [!div class="nextstepaction"]
> [Azure Data Share 概念](terminology.md)
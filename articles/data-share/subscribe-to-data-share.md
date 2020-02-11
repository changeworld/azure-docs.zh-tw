---
title: 教學課程：接受和接收資料 - Azure Data Share
description: 教學課程 - 使用 Azure Data Share 接受和接收資料
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: f2acb89597ef877543a2c4cc46f395aede41034b
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964493"
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
* 註冊 Microsoft.DataShare 資源提供者。 請依照[對 Azure Data Share 進行疑難排解](data-share-troubleshoot.md)中的指示，註冊資料共用資源提供者。

### <a name="receive-data-into-a-storage-account"></a>將資料接收到儲存體帳戶中： 

* Azure 儲存體帳戶：如果您還沒有此帳戶，則可以建立 [Azure 儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)。 
* 寫入儲存體帳戶的權限，存在於 Microsoft.Storage/storageAccounts/write  中。 此權限存在於參與者角色中。 
* 將角色指派新增至儲存體帳戶的權限，存在於 Microsoft.Authorization/role assignments/write  中。 此權限存在於擁有者角色中。  

### <a name="receive-data-into-a-sql-based-source"></a>將資料接收到 SQL 型來源：

* 對 SQL Server 上的資料庫進行寫入的權限，存在於 Microsoft.Sql/servers/databases/write  中。 此權限存在於參與者角色中。 
* 資料共用資源的受控識別用來存取 Azure SQL Database 或 Azure SQL 資料倉儲的權限。 這可以透過下列步驟完成： 
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
    1. 按一下 [+ 新增用戶端 IP]  ，然後按一下 [儲存]  。 用戶端 IP 位址可能會有所變更。 下次從 Azure 入口網站共用 SQL 資料時，可能需要重複執行此程序。 您也可以新增 IP 範圍。 


### <a name="receive-data-into-an-azure-data-explorer-cluster"></a>將資料接收到 Azure 資料總管叢集中： 

* 與資料提供者的資料總管叢集位於相同 Azure 資料中心的 Azure 資料總管叢集：如果您還沒有此叢集，可以建立 [Azure 資料總管叢集](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal)。 如果您不知道資料提供者叢集的 Azure 資料中心，您可以在稍後的程序中建立叢集。
* 寫入 Azure 資料總管叢集的權限，存在於 Microsoft.Kusto/clusters/write  中。 此權限存在於參與者角色中。 
* 將角色指派新增至 Azure 資料總管叢集的權限，存在於 Microsoft.Authorization/role assignments/write  中。 此權限存在於擁有者角色中。 

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="open-invitation"></a>開啟邀請

1. 檢查收件匣中是否有來自資料提供者的邀請。 邀請會來自 Microsoft Azure，標題為**來自 <yourdataprovider@domain.com> 的 Azure Data Share 邀請**。 記下共用名稱，以確保您可以在有多個邀請的情況下接受正確的共用。 

1. 選取 [檢視邀請]  以查看您在 Azure 中的邀請。 這會帶您前往 [已接收的共用] 檢視。

   ![邀請](./media/invitations.png "邀請清單") 

1. 選取您想要檢視的共用。 

## <a name="accept-invitation"></a>接受邀請
1. 請確定您已檢閱過所有欄位，包括 [使用規定]  。 如果您同意使用規定，則必須核取該方塊以表示您同意。 

   ![使用規定](./media/terms-of-use.png "使用規定") 

1. 在 [目標 Data Share 帳戶]  底下，選取要作為 Data Share 部署目的地的訂用帳戶和資源群組。 

   針對 [Data Share 帳戶]  欄位，如果您沒有現有的 Data Share 帳戶，請選取 [新建]  。 否則，請選取您想要用來接受資料共用的現有 Data Share 帳戶。 

   針對 [已收到的共用名稱]  欄位，您可以保留資料提供者所指定的預設值，也可以為已接收的共用指定新名稱。 

   ![目標 Data Share 帳戶](./media/target-data-share.png "目標 Data Share 帳戶") 

1. 同意使用規定並指定了共用的位置後，請選取 [接受並設定]  。 此時會建立共用訂用帳戶。

   若要進行以快照集為基礎的共用，則下一個畫面會要求您選取目標儲存體帳戶作為複製資料的目的地。 

   ![接受選項](./media/accept-options.png "接受選項") 

   如果您想要立即接受邀請，但稍後再設定目標資料存放區，請選取 [接受並稍後再設定]  。 若要稍後再繼續設定儲存體，請參閱[設定資料集對應](how-to-configure-mapping.md)頁面，以取得如何繼續設定資料共用的詳細步驟。 

   若要進行就地共用，請參閱[設定資料集對應](how-to-configure-mapping.md)頁面，以取得如何繼續設定資料共用的詳細步驟。 

   如果您不想要接受邀請，請選取 [拒絕]  。 

## <a name="configure-storage"></a>設定儲存體
1. 在 [目標儲存體設定]  底下，選取您想要用來接收資料的訂用帳戶、資源群組和儲存體帳戶。 

   ![目標儲存體設定](./media/target-storage-settings.png "目標儲存體") 

1. 若要接收資料的定期更新，請務必啟用快照集設定。 請注意，您只會看到一個快照集設定排程 (如果資料提供者已將該設定納入資料共用)。 

   ![快照集設定](./media/snapshot-settings.png "快照集設定") 

1. 選取 [儲存]  。 

> [!IMPORTANT]
> 如果您要接收 SQL 型資料，而且想要將該資料接收到 SQL 型來源，請瀏覽[設定資料集對應](how-to-configure-mapping.md)操作指南，以了解如何將 SQL Server 設定為資料集的目的地。 

## <a name="trigger-a-snapshot"></a>觸發快照集
這些步驟僅適用於以快照集為基礎的共用。

1. 您可以在 [已接收的共用] -> [詳細資料] 索引標籤中，藉由選取 [觸發快照集]  來觸發快照集。 在這裡，您可以觸發資料的完整或累加快照集。 如果您是第一次接收來自資料提供者的資料，請選取 [完整複本]。 

   ![觸發快照集](./media/trigger-snapshot.png "觸發快照集") 

1. 當最後一次執行的狀態為「成功」  時，請移至目標資料存放區檢視已接收的資料。 選取 [資料集]  ，然後按一下 [目標路徑] 中的連結。 

   ![取用者資料集](./media/consumer-datasets.png "取用者資料集對應") 

## <a name="view-history"></a>檢視歷程記錄
若要檢視快照集的歷程記錄，請瀏覽至 [已接收的共用] -> [歷程記錄]。 您會在這裡找到過去 60 天所有產生的快照集歷程記錄。 

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已了解如何接受和接收 Azure Data Share。 若要深入了解 Azure Data Share 概念，請繼續閱讀[概念：Azure Data Share 術語](terminology.md)。
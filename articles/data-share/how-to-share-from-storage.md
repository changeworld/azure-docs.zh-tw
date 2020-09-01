---
title: 從 Azure Blob 儲存體和 Azure Data Lake Storage 共用及接收資料
description: 瞭解如何從 Azure Blob 儲存體和 Azure Data Lake Storage 共用及接收資料
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 08/28/2020
ms.openlocfilehash: a54a9d4c50852fe78fd245723dd29f487d58f4b1
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89270048"
---
# <a name="share-and-receive-data-from-azure-blob-storage-and-azure-data-lake-storage"></a>從 Azure Blob 儲存體和 Azure Data Lake Storage 共用及接收資料

[!INCLUDE[appliesto-storage](includes/appliesto-storage.md)]

Azure Data Share 支援來自儲存體帳戶的快照式共用。 本文說明如何共用和接收來自下列來源的資料： Azure Blob 儲存體、Azure Data Lake Storage Gen1 Azure Data Lake Storage Gen2。

Azure Data Share 支援 Azure Data Lake Gen1 和 Azure Data Lake Gen2 共用檔案、資料夾和檔案系統。 它也支援從 Azure Blob 儲存體共用 blob、資料夾和容器。 目前僅支援區塊 blob。 從這些來源共用的資料可以接收 Azure Data Lake Gen2 或 Azure Blob 儲存體。

當檔案系統、容器或資料夾在快照集型共用中共用時，資料取用者可以選擇建立共用資料的完整複本，或利用累加式快照集功能只複製新的或更新的檔案。 增量快照集會以檔案的上次修改時間為基礎。 將會覆寫具有相同名稱的現有檔案。

## <a name="share-data"></a>共用資料

### <a name="prerequisites-to-share-data"></a>共用資料的必要條件

* Azure 訂用帳戶：如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
* 您收件者的 Azure 登入電子郵件地址 (不能使用其電子郵件別名)。
* 如果來源 Azure 資料存放區位於與您將用來建立資料共用資源的不同 Azure 訂用帳戶中，請在 Azure 資料存放區所在的訂用帳戶中註冊 [Microsoft.DataShare 資源提供者](concepts-roles-permissions.md#resource-provider-registration)。 

### <a name="prerequisites-for-source-storage-account"></a>來源儲存體帳戶的必要條件

* Azure 儲存體帳戶：如果您還沒有帳戶，可以建立一個 [Azure 儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* 寫入儲存體帳戶的權限，存在於 Microsoft.Storage/storageAccounts/write  中。 此權限存在於參與者角色中。
* 將角色指派新增至儲存體帳戶的權限，存在於 Microsoft.Authorization/role assignments/write  中。 此權限存在於擁有者角色中。 

### <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

### <a name="create-a-data-share-account"></a>建立 Data Share 帳戶

在 Azure 資源群組中建立 Azure Data Share 資源。

1. 選取入口網站左上角的 [功能表] 按鈕，然後選取 [ **建立資源** ] (+) 。

1. 搜尋「Data Share」**。

1. 選取 Data Share 然後選取 [ **建立**]。

1. 使用下列資訊填寫 Azure Data Share 資源的基本詳細資料。 

     **設定** | **建議的值** | **欄位描述**
    |---|---|---|
    | 訂用帳戶 | 您的訂用帳戶 | 選取您要用於資料共用帳戶的 Azure 訂用帳戶。|
    | 資源群組 | *test-resource-group* | 使用現有資源群組，或建立新的資源群組。 |
    | Location | *美國東部 2* | 選取資料共用帳戶的區域。
    | 名稱 | *datashareaccount* | 指定資料共用帳戶的名稱。 |
    | | |

1. 選取 [檢閱 + 建立]，然後選取 [建立] 來佈建您的資料共用帳戶。 佈建新的資料共用帳戶一般需要大約不到 2 分鐘的時間。 

1. 部署完成後，請選取 [移至資源]  。

### <a name="create-a-share"></a>建立共用

1. 瀏覽至 [Data Share 概觀] 頁面。

    ![共用您的資料](./media/share-receive-data.png "共用您的資料") 

1. 選取 [開始共用資料]。

1. 選取 [建立]  。   

1. 填寫您共用的詳細資料。 指定名稱、共用類型、共用內容的說明和使用規定 (選擇性)。 

    ![EnterShareDetails](./media/enter-share-details.png "輸入共用詳細資料") 

1. 選取 [繼續]。

1. 若要將資料集新增至您的共用，請選取 [ **新增資料集**]。 

    ![將資料集新增至您的共用](./media/datasets.png "資料集")

1. 選取您想要新增的資料集類型。 根據您在先前的步驟中選取的共用類型 (快照集或就地)，您將看到不同的資料集類型清單。 

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

Azure Data Share 現已建立完成，而且 Data Share 的收件者現已準備好接受邀請。 

## <a name="receive-data"></a>接收資料

### <a name="prerequisites-to-receive-data"></a>接收資料的必要條件
您必須先佈建一些 Azure 資源 (下面會列出) 才可以接受資料共用邀請。 

請先確定您已完成所有必要條件再接受資料共用邀請。 

* Azure 訂用帳戶：如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
* 資料共用邀請：Microsoft Azure 所送來的邀請，主旨標題為「來自 **<yourdataprovider@domain.com>** 的 Azure Data Share 邀請」。
* 在您將建立資料共用資源的 Azure 訂用帳戶及目標 Azure 資料存放區所在的 Azure 訂用帳戶中，註冊 [Microsoft.DataShare 資源提供者](concepts-roles-permissions.md#resource-provider-registration)。

### <a name="prerequisites-for-target-storage-account"></a>目標儲存體帳戶的必要條件

* Azure 儲存體帳戶：如果您還沒有此帳戶，則可以建立 [Azure 儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)。 
* 寫入儲存體帳戶的權限，存在於 Microsoft.Storage/storageAccounts/write  中。 此權限存在於參與者角色中。 
* 將角色指派新增至儲存體帳戶的權限，存在於 Microsoft.Authorization/role assignments/write  中。 此權限存在於擁有者角色中。  

### <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

### <a name="open-invitation"></a>開啟邀請

1. 您可以從電子郵件或直接從 Azure 入口網站開啟邀請。 

   若要從電子郵件開啟邀請，請檢查您的收件匣是否有來自您資料提供者的邀請。 邀請會來自 Microsoft Azure，標題為**來自 <yourdataprovider@domain.com> 的 Azure Data Share 邀請**。 按一下 [檢視邀請]  以查看您在 Azure 中的邀請。 

   若要直接從 Azure 入口網站開啟邀請，請在 Azure 入口網站中搜尋 **Data Share 邀請**。 這會帶您前往 Data Share 邀請的清單。

   ![邀請清單](./media/invitations.png "邀請清單") 

1. 選取您想要檢視的共用。 

### <a name="accept-invitation"></a>接受邀請
1. 請確定您已檢閱過所有欄位，包括 [使用規定]  。 如果您同意使用規定，則必須核取該方塊以表示您同意。 

   ![使用規定](./media/terms-of-use.png "使用規定") 

1. 在 [目標 Data Share 帳戶]  底下，選取要作為 Data Share 部署目的地的訂用帳戶和資源群組。 

   針對 [Data Share 帳戶]  欄位，如果您沒有現有的 Data Share 帳戶，請選取 [新建]  。 否則，請選取您想要用來接受資料共用的現有 Data Share 帳戶。 

   針對 [已收到的共用名稱]  欄位，您可以保留資料提供者所指定的預設值，也可以為已接收的共用指定新名稱。 

   同意使用規定並指定了管理收到之共用的資料共用帳戶後，請選取 [接受並設定]。 此時會建立共用訂用帳戶。 

   ![接受選項](./media/accept-options.png "接受選項") 

   同時會帶您前往資料共用帳戶中所接收的共用。 

   如果您不想要接受邀請，請選取 [拒絕]**。 

### <a name="configure-received-share"></a>設定已接收的共用
請遵循下列步驟來設定要接收資料的位置。

1. 選取**資料集**索引標籤。勾選要指派目的地之資料集旁的方塊。 選取 [+ 對應至目標] 以選擇目標資料存放區。 

   ![對應到目標](./media/dataset-map-target.png "對應到目標") 

1. 選取您希望資料進入的目標資料存放區。 將會覆寫目標資料存放區中具有相同路徑和名稱的所有資料檔案。 

   ![目標儲存體帳戶](./media/map-target.png "目標儲存體") 

1. 對於以快照集為基礎的共用，如果資料提供者已建立快照集排程來定期更新資料，也可以選取 [快照集排程] 索引標籤來啟用快照集排程。勾選快照集排程旁的方塊，然後選取 [+ 啟用]。

   ![啟用快照集排程](./media/enable-snapshot-schedule.png "啟用快照集排程")

### <a name="trigger-a-snapshot"></a>觸發快照集
這些步驟僅適用於以快照集為基礎的共用。

1. 您可以選取 [觸發程序快照集]及 [詳細資料] 索引標籤以觸發快照集。 在這裡，您可以觸發資料的完整或累加快照集。 如果您是第一次接收來自資料提供者的資料，請選取 [完整複本]。 

   ![觸發快照集](./media/trigger-snapshot.png "觸發快照集") 

1. 當最後一次執行的狀態為「成功」** 時，請移至目標資料存放區檢視已接收的資料。 選取 [資料集]****，然後按一下 [目標路徑] 中的連結。 

   ![取用者資料集](./media/consumer-datasets.png "取用者資料集對應") 

### <a name="view-history"></a>檢視歷程記錄
此步驟僅適用於以快照集為基礎的共用。 若要檢視快照集的記錄，請選取 [歷程記錄] 索引標籤。您會在這裡找到過去 30 天所有產生的快照集歷程記錄。 

## <a name="next-steps"></a>後續步驟
您已瞭解如何使用 Azure Data Share 服務，從儲存體帳戶共用及接收資料。 若要深入瞭解如何從其他資料來源共用，請繼續進行 [支援的資料存放區](supported-data-stores.md)。


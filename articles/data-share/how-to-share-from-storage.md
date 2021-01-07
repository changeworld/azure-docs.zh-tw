---
title: 從 Azure Blob 儲存體和 Azure Data Lake Storage 共用和接收資料
description: 瞭解如何從 Azure Blob 儲存體和 Azure Data Lake Storage 共用及接收資料。
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 12/16/2020
ms.openlocfilehash: 242980ac1b89345ed9d8ff903e65129cff3cb917
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964094"
---
# <a name="share-and-receive-data-from-azure-blob-storage-and-azure-data-lake-storage"></a>從 Azure Blob 儲存體和 Azure Data Lake Storage 共用和接收資料

[!INCLUDE[appliesto-storage](includes/appliesto-storage.md)]

Azure Data Share 支援從儲存體帳戶以快照集為基礎的共用。 本文說明如何共用和接收來自 Azure Blob 儲存體、Azure Data Lake Storage Gen1 和 Azure Data Lake Storage Gen2 的資料。

Azure Data Share 支援從 Azure Data Lake Gen1 和 Azure Data Lake Gen2 共用檔案、資料夾和檔案系統。 它也支援從 Azure Blob 儲存體共用 blob、資料夾和容器。 目前僅支援區塊 blob。 Azure Data Lake Gen2 或 Azure Blob 儲存體可以接收來自這些來源的共用資料。

當檔案系統、容器或資料夾在以快照集為基礎的共用中共用時，資料取用者可以選擇建立共用資料的完整複本。 或者，他們可以使用累加式快照集功能，僅複製新的或更新的檔案。 增量快照集功能是以檔案的上次修改時間為基礎。 

快照集期間會覆寫具有相同名稱的現有檔案。 未在目標上刪除從來源刪除的檔案。 來源的空白子資料夾不會複製到目標。 
## <a name="share-data"></a>共用資料

使用下列各節中的資訊，透過 Azure Data Share 共用資料。 
### <a name="prerequisites-to-share-data"></a>共用資料的必要條件

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
* 尋找您收件者的 Azure 登入電子郵件地址。 收件者的電子郵件別名不適用於您的用途。
* 如果來源 Azure 資料存放區與您將在其中建立 Data Share 資源的 Azure 訂用帳戶不同，請在 Azure 資料存放區所在的訂用帳戶中註冊 [microsoft.datashare 資源提供者](concepts-roles-permissions.md#resource-provider-registration) 。 

### <a name="prerequisites-for-the-source-storage-account"></a>來源儲存體帳戶的必要條件

* Azure 儲存體帳戶。 如果您還沒有帳戶，請 [建立一個](../storage/common/storage-account-create.md)帳戶。
* 寫入儲存體帳戶的許可權。 寫入權限位於 *Microsoft. Storage/storageAccounts/write*。 它是參與者角色的一部分。
* 將角色指派新增至儲存體帳戶的許可權。 這是 Microsoft 的 *授權/角色指派/寫入* 許可權。 它是擁有者角色的一部分。 

### <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

### <a name="create-a-data-share-account"></a>建立 Data Share 帳戶

在 Azure 資源群組中建立 Azure Data Share 資源。

1. 在入口網站的左上角，開啟功能表，然後選取 [ **建立資源** ] (+) 。

1. 搜尋「Data Share」  。

1. 選取 **Data Share** 並 **建立**。

1. 提供 Azure Data Share 資源的基本詳細資料： 

     **設定** | **建議的值** | **欄位描述**
    |---|---|---|
    | 訂用帳戶 | 您的訂用帳戶 | 為您的資料共用帳戶選取 Azure 訂用帳戶。|
    | 資源群組 | *test-resource-group* | 使用現有的資源群組或建立資源群組。 |
    | Location | *美國東部 2* | 選取資料共用帳戶的區域。
    | 名稱 | *datashareaccount* | 命名您的資料共用帳戶。 |
    | | |

1. 選取 [**審核 + 建立**  >  **建立**] 以布建您的資料共用帳戶。 布建新的資料共用帳戶通常需要大約2分鐘的時間。 

1. 部署完成後，選取 [前往資源]。

### <a name="create-a-share"></a>建立共用

1. 移至您的 data share **總覽** 頁面。

   :::image type="content" source="./media/share-receive-data.png" alt-text="顯示資料共用總覽的螢幕擷取畫面。":::

1. 選取 [開始共用資料]。

1. 選取 [建立]  。   

1. 提供共用的詳細資料。 指定名稱、共用類型、共用內容的說明和使用規定 (選擇性)。 

    ![顯示資料共用詳細資料的螢幕擷取畫面。](./media/enter-share-details.png "輸入資料共用詳細資料。") 

1. 選取 [繼續]。

1. 若要將資料集新增至您的共用，請選取 [ **新增資料集**]。 

    ![顯示如何將資料集新增至共用的螢幕擷取畫面。](./media/datasets.png "資料集。")

1. 選取要新增的資料集類型。 資料集類型清單取決於您在上一個步驟中選取的是以快照集為基礎的共用或就地共用。 

    ![顯示要在哪裡選取資料集類型的螢幕擷取畫面。](./media/add-datasets.png "新增資料集。")    

1. 移至您想要共用的物件。 然後選取 [ **新增資料集**]。 

    ![顯示如何選取要共用之物件的螢幕擷取畫面。](./media/select-datasets.png "選取資料集。")    

1. 在 [收件者] 索引標籤上， **選取 [** **加入收件** 者] 以新增資料取用者的電子郵件地址 

    ![顯示如何新增收件者電子郵件地址的螢幕擷取畫面。](./media/add-recipient.png "加入收件者。") 

1. 選取 [繼續]。

1. 如果您選取了快照集共用類型，則可以設定快照集排程，以更新資料取用者的資料。 

    ![顯示快照集排程設定的螢幕擷取畫面。](./media/enable-snapshots.png "啟用快照集。") 

1. 選取開始時間和週期間隔。 

1. 選取 [繼續]。

1. 在 [ **審核 + 建立** ] 索引標籤上，檢查您的套件內容、設定、收件者和同步處理設定。 然後選取 [建立]。

您現在已建立 Azure data share。 資料共用的收件者可以接受您的邀請。 

## <a name="receive-data"></a>接收資料

下列各節說明如何接收共用資料。
### <a name="prerequisites-to-receive-data"></a>接收資料的必要條件
接受資料共用邀請之前，請確定您具有下列必要條件： 

* Azure 訂用帳戶。 如果您沒有訂用帳戶，請建立 [免費帳戶](https://azure.microsoft.com/free/)。
* Azure 的邀請。 電子郵件主旨應該是「Azure Data Share 邀請」 *\<yourdataprovider\@domain.com>* 。
* 中已註冊的 [microsoft.datashare 資源提供者](concepts-roles-permissions.md#resource-provider-registration) ：
    * 您將在其中建立 Data Share 資源的 Azure 訂用帳戶。
    * 目標 Azure 資料存放區所在的 Azure 訂用帳戶。

### <a name="prerequisites-for-a-target-storage-account"></a>目標儲存體帳戶的必要條件

* Azure 儲存體帳戶。 如果您還沒有帳戶，請 [建立一個帳戶](../storage/common/storage-account-create.md)。 
* 寫入儲存體帳戶的許可權。 此許可權位於 *Microsoft. Storage/storageAccounts/write* 中。 它是參與者角色的一部分。 
* 將角色指派新增至儲存體帳戶的許可權。 這項指派是在 *Microsoft 中。授權/角色指派/寫入*。 它是擁有者角色的一部分。  

### <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

### <a name="open-an-invitation"></a>開啟邀請

您可以從電子郵件或直接從 Azure 入口網站開啟邀請。

1. 若要從電子郵件開啟邀請，請檢查您的收件匣是否有來自您的資料提供者的邀請。 Microsoft Azure 的邀請標題為「Azure Data Share 邀請」 *\<yourdataprovider\@domain.com>* 。 選取 [ **View 邀請** ] 以查看您在 Azure 中的邀請。 

   若要從 Azure 入口網站開啟邀請，請搜尋 *Data Share 邀請*。 您會看到 Data Share 的邀請清單。

   ![顯示 Azure 入口網站中的邀請清單的螢幕擷取畫面。](./media/invitations.png "邀請清單。") 

1. 選取您要查看的共用。 

### <a name="accept-an-invitation"></a>接受邀請
1. 檢查所有欄位，包括 **使用規定**。 如果您同意條款，請選取核取方塊。 

   ![顯示使用規定區域的螢幕擷取畫面。](./media/terms-of-use.png "使用條款。") 

1. 在 [ **目標 Data Share 帳戶**] 底下，選取您要部署 Data Share 的訂用帳戶和資源群組。 接著填寫下列欄位：

   * 如果您沒有 Data Share 帳戶，請在 [ **資料共用帳戶** ] 欄位中，選取 [ **建立新** 的]。 否則，請選取將接受您的資料共用的現有 Data Share 帳戶。 

   * 在 [ **已接收的共用名稱** ] 欄位中，保留資料提供者所指定的預設值，或為已接收的共用指定新名稱。 

1. 選取 [ **接受並設定**]。 建立共用訂用帳戶。 

   ![顯示要在哪裡接受設定選項的螢幕擷取畫面。](./media/accept-options.png "接受選項") 

    收到的共用會出現在您的 Data Share 帳戶中。 

    如果您不想接受邀請，請選取 [ **拒絕**]。 

### <a name="configure-a-received-share"></a>設定已接收的共用
遵循本節中的步驟，設定要接收資料的位置。

1. 在 [ **資料集** ] 索引標籤上，選取您要指派目的地之資料集旁邊的核取方塊。 選取 [ **對應到目標** ] 以選擇目標資料存放區。 

   ![顯示如何對應至目標的螢幕擷取畫面。](./media/dataset-map-target.png "對應至目標。") 

1. 選取資料的目標資料存放區。 目標資料存放區中，與所接收資料中檔案具有相同路徑和名稱的檔案將會遭到覆寫。 

   ![顯示要在哪裡選取目標儲存體帳戶的螢幕擷取畫面。](./media/map-target.png "目標儲存體。") 

1. 如果是以快照集為基礎的共用，如果資料提供者使用快照集排程來定期更新資料，您可以從 [ **快照集排程** ] 索引標籤啟用排程。選取快照集排程旁的方塊。 接著選取 [啟用]。

   ![顯示如何啟用快照集排程的螢幕擷取畫面。](./media/enable-snapshot-schedule.png "啟用快照集排程。")

### <a name="trigger-a-snapshot"></a>觸發快照集
本節中的步驟僅適用于以快照集為基礎的共用。

1. 您可以從 [ **詳細資料** ] 索引標籤觸發快照集。在索引標籤上，選取 [ **觸發快照** 集]。 您可以選擇觸發資料的完整快照集或增量快照集。 如果您是第一次從資料提供者收到資料，請選取 [ **完整複製**]。 

   ![顯示觸發程式快照選取專案的螢幕擷取畫面。](./media/trigger-snapshot.png "觸發快照集。") 

1. 當最後一次執行的狀態為 *成功* 時，請移至目標資料存放區以查看接收的資料。 選取 [ **資料集**]，然後選取 [目標路徑] 連結。 

   ![顯示取用者資料集對應的螢幕擷取畫面。](./media/consumer-datasets.png "取用者資料集對應。") 

### <a name="view-history"></a>檢視歷程記錄
您只能在快照式共用中查看快照集的歷程記錄。 若要查看歷程記錄，請開啟 [歷程 **記錄** ] 索引標籤。在這裡，您會看到過去30天內產生的所有快照集的歷程記錄。 

## <a name="next-steps"></a>後續步驟
您已瞭解如何使用 Azure Data Share 服務，從儲存體帳戶共用及接收資料。 若要瞭解如何從其他資料來源共用，請參閱 [支援的資料存放區](supported-data-stores.md)。
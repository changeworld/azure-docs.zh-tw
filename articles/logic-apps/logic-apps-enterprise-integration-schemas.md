---
title: 使用架構驗證 XML
description: 新增結構描述，以在採用 Enterprise Integration Pack 的 Azure Logic Apps 中驗證 XML 文件
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/06/2019
ms.openlocfilehash: ade29318fdd1510d03ddd208534258a30b256e98
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75979367"
---
# <a name="validate-xml-with-schemas-in-azure-logic-apps-with-enterprise-integration-pack"></a>在採用 Enterprise Integration Pack 的 Azure Logic Apps 中使用結構描述來驗證 XML

若要確認文件使用有效的 XML ，且其預期資料採用針對 Azure Logic Apps 中企業整合案例預先定義的格式，您的邏輯應用程式可以使用結構描述。 結構描述也可以驗證邏輯應用程式在企業對企業 (B2B) 案例中交換的訊息。

如需整合帳戶和成品 (例如結構描述) 的相關限制，請參閱 [Azure Logic Apps 的限制與設定資訊](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有訂用帳戶，請<a href="https://azure.microsoft.com/free/" target="_blank">註冊一個免費的 Azure 帳戶</a>。

* [整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)，您在其中為企業整合和企業對企業 (B2B) 解決方案儲存您的結構描述與其他成品。 

  若您的結構描述為 [2 MB 或更小](#smaller-schema)，您可以直接從 Azure 入口網站將結構描述新增到您的整合帳戶。 不過，如果結構描述大於 2 MB，但未大於[結構描述大小限制](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)，您即可將結構描述上傳到 Azure 儲存體帳戶。 
  若要將該結構描述新增至您的整合帳戶，您可以接著從您的整合帳戶連結至儲存體帳戶。 
  對於這項工作，以下是您需要的項目： 

  * [Azure 儲存體帳戶](../storage/common/storage-account-overview.md)，您在其中為您的結構描述建立 Blob 容器。 了解如何[建立儲存體帳戶](../storage/common/storage-account-create.md)。 

  * 用來儲存結構描述的 Blob 容器。 了解如何[建立 Blob 容器](../storage/blobs/storage-quickstart-blobs-portal.md)。 
  當您將結構描述新增到您的整合帳戶時，您需要容器的內容 URI。

  * [Azure 儲存體總管](../vs-azure-tools-storage-manage-with-storage-explorer.md)，您可用來管理儲存體帳戶和 Blob 容器。 
  若要使用儲存體總管，請選擇以下其中一個選項：
  
    * 在 Azure 入口網站中，尋找並選取您的儲存體帳戶。 
    從您的儲存體帳戶功能表，選取 [儲存體總管]****。

    * 對於傳統型版本，[下載並安裝 Azure 儲存體總管](https://www.storageexplorer.com/)。 
    接著，依照[開始使用儲存體總管](../vs-azure-tools-storage-manage-with-storage-explorer.md) [儲存體總管] 連線到您的儲存體帳戶。 
    若要深入瞭解，請參閱[快速入門：使用 Azure 儲存體總管在物件儲存體中建立 blob](../storage/blobs/storage-quickstart-blobs-storage-explorer.md)。

建立並新增結構描述時，不需要邏輯應用程式。 不過，若要使用結構描述，您的邏輯應用程式必須連結到您在其中儲存結構描述的整合帳戶。 了解[如何將邏輯應用程式連結到整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)。 如果您尚未擁有邏輯應用程式，請了解[如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="add-schemas"></a>新增結構描述

1. 使用您的 Azure 帳戶認證登入 <a href="https://portal.azure.com" target="_blank">Azure 入口網站</a>。

1. 若要尋找並開啟您的整合帳戶，請開啟主 Azure 功能表，然後選取 [所有服務]****。 在搜尋方塊中，輸入「整合帳戶」。 選取 [**整合帳戶**]。

   ![尋找整合帳戶](./media/logic-apps-enterprise-integration-schemas/find-integration-account.png)

1. 選取要將結構描述新增到其中的整合帳戶，例如：

   ![選取企業整合帳戶](./media/logic-apps-enterprise-integration-schemas/select-integration-account.png)

1. 在您的整合帳戶的 [概觀]**** 頁面上的 [元件]**** 下，選取 [結構描述]**** 圖格。

   ![選取 [結構描述]](./media/logic-apps-enterprise-integration-schemas/select-schemas.png)

1. 在 [結構描述]**** 頁面開啟之後，選擇 [新增]****。

   ![選擇 [新增]](./media/logic-apps-enterprise-integration-schemas/add-schema.png)

根據您的結構描述 (.xsd) 檔案大小，依照相關步驟上傳[最多 2 MB](#smaller-schema) 或[超過 2 MB 但最多 8 MB](#larger-schema) 的結構描述。

<a name="smaller-schema"></a>

### <a name="add-schemas-up-to-2-mb"></a>新增最多 2 MB 的結構描述

1. 在 [新增結構描述]**** 下方，輸入結構描述的名稱。 
   維持選取 [小型檔案]****。 在 [結構描述]**** 方塊旁，選擇資料夾圖示。 尋找並選取您要上傳的結構描述，例如：

   ![上傳小型結構描述](./media/logic-apps-enterprise-integration-schemas/upload-smaller-schema-file.png)

1. 當您就緒時，請選擇 [確定]****。

   當您的結構描述完成上傳之後，結構描述會出現在 [結構描述]**** 清單中。

<a name="larger-schema"></a>

### <a name="add-schemas-more-than-2-mb"></a>新增超過 2 MB 的結構描述

若要新增較大的結構描述，您可以在您的 Azure 儲存體帳戶中將結構描述上傳到 Azure Blob 容器。 您新增架構的步驟會根據您的 blob 容器是否有公用讀取權限而有所不同。 因此，請先依照下列步驟檢查您的 blob 容器是否有公用讀取權限：[設定 blob 容器的公用存取層級](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>檢查容器存取層級

1. 開啟 [Azure 儲存體總管]。 在 [總管] 視窗中，展開您的 Azure 訂用帳戶 (若尚未展開)。

1. 展開 [儲存體帳戶]**** > {*您的儲存體帳戶*} > [Blob 容器]****。 選取您的 Blob 容器。

1. 從 Blob 容器的快顯功能表，選取 [設定公用存取層級]****。

   * 如果您的 blob 容器至少具有公用存取權，請選擇 [**取消**]，並遵循此頁面稍後的下列步驟：[上傳至具有公用存取權的容器](#public-access)

     ![公用存取權](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * 如果您的 blob 容器沒有公用存取權，請選擇 [**取消**]，並遵循此頁面稍後的下列步驟：[上傳至沒有公用存取權的容器](#public-access)

     ![沒有公用存取權](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access"></a>

#### <a name="upload-to-containers-with-public-access"></a>上傳到具有公用存取權的容器

1. 將結構描述上傳至儲存體帳戶。 
   在右邊的視窗中，選擇 [上傳]****。

1. 完成上傳之後，請選取您已上傳的結構描述。 在工具列上，選擇 [複製 URL]**** 以複製結構描述的 URL。

1. 返回 Azure 入口網站，其中 [新增結構描述]**** 窗格已開啟。 
   輸入您的組件名稱。 
   選擇 [大型檔案 (大於 2MB)]****。 

   現在會出現 [內容 URI]**** 方塊，而非 [結構描述]**** 方塊。

1. 在 [內容 URI]**** 方塊中，貼上您的結構描述 URL。 
   尋找並新增您的結構描述。

當您的結構描述完成上傳之後，結構描述會出現在 [結構描述]**** 清單中。 在您整合帳戶的 [概觀]**** 頁面上的 [元件]**** 下，[結構描述]**** 圖格現在會顯示已上傳的結構描述數目。

<a name="no-public-access"></a>

#### <a name="upload-to-containers-without-public-access"></a>上傳到沒有公用存取權的容器

1. 將結構描述上傳至儲存體帳戶。 
   在右邊的視窗中，選擇 [上傳]****。

1. 完成上傳之後，為您的結構描述產生共用存取簽章 (SAS)。 
   從結構描述的快顯功能表，選取 [取得共用存取簽章]****。

1. 在 [共用存取簽章]**** 窗格中，選取 [產生容器層級共用存取簽章 URI]**** > [建立]****。 
   產生 SAS URL 之後，選擇 [URL]**** 方塊旁的 [複製]****。

1. 返回 Azure 入口網站，其中 [新增結構描述]**** 窗格已開啟。 選擇 [大型檔案]****。

   現在會出現 [內容 URI]**** 方塊，而非 [結構描述]**** 方塊。

1. 在 [內容 URI]**** 方塊中，貼上您先前產生的 SAS URI。 尋找並新增您的結構描述。

當您的結構描述完成上傳之後，結構描述會出現在 [結構描述]**** 清單中。 在您整合帳戶的 [概觀]**** 頁面上的 [元件]**** 下，[結構描述]**** 圖格現在會顯示已上傳的結構描述數目。

## <a name="edit-schemas"></a>編輯結構描述

若要更新現有的結構描述，您必須上傳包含您所要變更的新結構描述檔案。 不過，您可以先下載現有的結構描述來編輯。

1. 在 <a href="https://portal.azure.com" target="_blank">Azure 入口網站</a>中，尋找並開啟您的整合帳戶 (若它尚未開啟)。

1. 在主要 Azure 功能表上，選取 [所有服務]****。 
   在搜尋方塊中，輸入「整合帳戶」。 
   選取 [**整合帳戶**]。

1. 選取要在其中更新結構描述的整合帳戶。

1. 在您的整合帳戶的 [概觀]**** 頁面上的 [元件]**** 下，選取 [結構描述]**** 圖格。

1. 在 [結構描述]**** 頁面開啟之後，選取您的結構描述。 
   若要先下載並編輯結構描述，請選擇 [下載]****，然後儲存結構描述。

1. 當您準備好上傳已更新的結構描述時，請在 [結構描述]**** 頁面上選取您要更新的結構描述，然後選擇 [更新]****。

1. 尋找並選取您要上傳的已更新結構描述。 
   當您的結構描述檔案完成上傳之後，已更新的結構描述會出現在 [結構描述]**** 清單中。

## <a name="delete-schemas"></a>刪除結構描述

1. 在 <a href="https://portal.azure.com" target="_blank">Azure 入口網站</a>中，尋找並開啟您的整合帳戶 (若它尚未開啟)。

1. 在主要 Azure 功能表上，選取 [所有服務]****。 
   在搜尋方塊中，輸入「整合帳戶」。 
   選取 [**整合帳戶**]。

1. 選取要在其中刪除結構描述的整合帳戶。

1. 在您的整合帳戶的 [概觀]**** 頁面上的 [元件]**** 下，選取 [結構描述]**** 圖格。

1. 在 [結構描述]**** 頁面開啟之後，選取您的結構描述，然後選擇 [刪除]****。

1. 若要確認您要刪除結構描述，請選擇 [是]****。

## <a name="next-steps"></a>後續步驟

* [深入了解企業整合套件](logic-apps-enterprise-integration-overview.md)
* [深入了解對應](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [深入了解轉換](../logic-apps/logic-apps-enterprise-integration-transform.md)

---
title: 使用 XSLT 對應轉換 XML
description: 使用 Enterprise Integration Pack 在 Azure Logic Apps 中新增 XSLT 對應以轉換 XML
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/06/2019
ms.openlocfilehash: ff6bd1a52e05d55d43969644141f39aed8c8eb63
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87072653"
---
# <a name="transform-xml-with-maps-in-azure-logic-apps-with-enterprise-integration-pack"></a>使用 Enterprise Integration Pack 在 Azure Logic Apps 中以對應來轉換 XML

若要在 Azure Logic Apps 中的企業整合案例的格式之間轉換 XML 資料，您的邏輯應用程式可以使用對應，或更具體而言，使用可延伸樣式表語言轉換 (XSLT) 對應。 對應是一個 XML 文件，它描述如何將資料從 XML 文件轉換為其他格式。 

例如，假設您會定期收到客戶的 B2B 訂單或發票，而該客戶使用 YYYMMDD 日期格式。 不過，您的組織使用 MMDDYYY 日期格式。 您可以定義並使用對應，先將 YYYMMDD 日期格式轉換為 MMDDYYY 格式，然後再將訂單或發票儲存在您的客戶活動資料庫中。

針對與整合帳戶和成品 (例如對應) 的限制，請參閱 [Azure Logic Apps 的限制與設定資訊](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="prerequisites"></a>先決條件

* Azure 訂用帳戶。 如果您沒有訂用帳戶，請[註冊一個免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* [整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)，您在其中為企業整合和企業對企業 (B2B) 解決方案儲存您的對應與其他成品。

* 若您的對應參考外部組件，您必須上傳*兩個組件與對應*到您的整合帳戶。 請務必[*先上傳您的元件*](#add-assembly)，然後上傳參考元件的對應。

  若您的組件小於 2 MB，您可以*直接*從 Azure 入口網站將您的組件新增到您的整合帳戶。 不過，若您的組件或對應大於 2 MB 但未大於[組件或對應的大小限制](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)，您可以使用下列選項：

  * 針對組件，您需要 Azure Blob 容器 (以在其中上傳您的組件) 與該容器的位置。 如此一來，您就可以在稍後將組件新增到您的整合帳戶時提供該位置。 
  針對此工作，您需要下列項目：

    | 項目 | 描述 |
    |------|-------------|
    | [Azure 儲存體帳戶](../storage/common/storage-account-overview.md) | 在此帳戶中，為您的組件建立 Azure Blob 容器。 瞭解[如何建立儲存體帳戶](../storage/common/storage-account-create.md)。 |
    | Blob 容器 | 在此容器中，您可以上傳您的組件。 當您將組件新增到您的整合帳戶時，您也需要此容器的位置。 了解如何[建立 Blob 容器](../storage/blobs/storage-quickstart-blobs-portal.md)。 |
    | [Azure 儲存體總管](../vs-azure-tools-storage-manage-with-storage-explorer.md) | 此工具可協助您更輕鬆地管理儲存體帳戶與 Blob 容器。 若要使用 [儲存體總管]，請[下載並安裝 Azure 儲存體總管](https://www.storageexplorer.com/)。 接著，依照[開始使用儲存體總管](../vs-azure-tools-storage-manage-with-storage-explorer.md) [儲存體總管] 連線到您的儲存體帳戶。 若要深入瞭解，請參閱[快速入門：使用 Azure 儲存體總管在物件儲存體中建立 blob](../storage/blobs/storage-quickstart-blobs-storage-explorer.md)。 <p>或者，在 Azure 入口網站中，尋找並選取您的儲存體帳戶。 從您的儲存體帳戶功能表，選取 [儲存體總管]****。 |
    |||

  * 針對對應，您目前可以使用 [Azure Logic Apps REST API - 對應](/rest/api/logic/maps/createorupdate)新增更大的對應。

建立並新增對應時，不需要邏輯應用程式。 不過，若要使用對應，您的邏輯應用程式必須連結到您在其中儲存對應的整合帳戶。 了解[如何將邏輯應用程式連結到整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)。 如果您尚未擁有邏輯應用程式，請了解[如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="add-assembly"></a>

## <a name="add-referenced-assemblies"></a>新增參考的組件

1. 使用您的 Azure 帳戶認證登入 [Azure 入口網站](https://portal.azure.com)。

1. 若要尋找並開啟您的整合帳戶，請開啟主 Azure 功能表，然後選取 [所有服務]****。 
   在搜尋方塊中，輸入「整合帳戶」。 
   選取 [**整合帳戶**]。

   ![尋找整合帳戶](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. 選取要將組件新增到其中的整合帳戶，例如：

   ![選取企業整合帳戶](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. 在您的整合帳戶的 [概觀]**** 頁面上的 [元件]**** 下，選取 [組件]**** 圖格。

   ![選取 [組件]](./media/logic-apps-enterprise-integration-maps/select-assemblies.png)

1. 在 [**元件**] 頁面開啟之後，選擇 [**新增**]。

   ![選擇 [新增]](./media/logic-apps-enterprise-integration-maps/add-assembly.png)

視您的組件檔案大小而定，依照[未超過 2 MB](#smaller-assembly) 或[超過 2 MB 但小於 8 MB](#larger-assembly) 的步驟上傳組件。
針對整合帳戶中的組件數量限制，請參閱 [Azure Logic Apps 的限制與設定](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)。

> [!NOTE]
> 如果您變更元件，不論對應是否有變更，您也必須更新對應。

<a name="smaller-assembly"></a>

### <a name="add-assemblies-up-to-2-mb"></a>新增大小未超過 2 MB 的組件

1. 在 [新增組件]**** 下，輸入您的組件名稱。 維持選取 [小型檔案]****。 在 [組件]**** 方塊旁，選擇資料夾圖示。 尋找並選取您要上傳的組件，例如：

   ![上傳小型組件](./media/logic-apps-enterprise-integration-maps/upload-assembly-file.png)

   在 [組件名稱]**** 屬性中，組件的檔案名稱會在您選取組件之後自動出現。

1. 當您就緒時，請選擇 [確定]****。

   當您的組件完成上傳之後，組件會出現在 [組件]**** 清單中。

   ![已上傳的組件清單](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies-list.png)

   在您整合帳戶的 [概觀]**** 頁面上的 [元件]**** 下，[組件]**** 圖格現在會顯示已上傳的組件數目，例如：

   ![已上傳的組件](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies.png)

<a name="larger-assembly"></a>

### <a name="add-assemblies-more-than-2-mb"></a>新增大小超過 2 MB 的組件

若要新增較大的組件，您可以在您的 Azure 儲存體帳戶中將您的組件上傳到 Azure Blob 容器。 您新增元件的步驟會根據您的 blob 容器是否有公用讀取權限而有所不同。 因此，請先依照下列步驟檢查您的 blob 容器是否有公用讀取權限：[設定 blob 容器的公用存取層級](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>檢查容器存取層級

1. 開啟 [Azure 儲存體總管]。 在 [總管] 視窗中，展開您的 Azure 訂用帳戶 (若尚未展開)。

1. 展開 [儲存體帳戶]**** > {*您的儲存體帳戶*} > [Blob 容器]****。 選取您的 Blob 容器。

1. 從 Blob 容器的快顯功能表，選取 [設定公用存取層級]****。

   * 如果您的 blob 容器至少具有公用存取權，請選擇 [**取消**]，並遵循此頁面稍後的下列步驟：[上傳至具有公用存取權的容器](#public-access-assemblies)

     ![公用存取](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * 如果您的 blob 容器沒有公用存取權，請選擇 [**取消**]，並遵循此頁面稍後的下列步驟：[上傳至沒有公用存取權的容器](#no-public-access-assemblies)

     ![沒有公用存取權](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-assemblies"></a>

#### <a name="upload-to-containers-with-public-access"></a>上傳到具有公用存取權的容器

1. 將組件上傳到您的儲存體帳戶。 
   在右邊的視窗中，選擇 [上傳]****。

1. 完成上傳之後，請選取您的已上傳組件。 在工具列上，選擇 [複製 URL]**** 以複製組件的 URL。

1. 返回 Azure 入口網站，其中 [新增組件]**** 窗格已開啟。 
   輸入您的組件名稱。 
   選擇 [大型檔案 (大於 2MB)]****。

   現在會出現 [內容 URI]**** 方塊，而非 [組件]**** 方塊。

1. 在 [內容 URI]**** 方塊中，貼上您的組件 URL。 
   尋找並新增您的組件。

當您的組件完成上傳之後，結構描述會出現在 [組件]**** 清單中。
在您整合帳戶的 [概觀]**** 頁面上的 [元件]**** 下，[組件]**** 圖格現在會顯示已上傳的組件數目。

<a name="no-public-access-assemblies"></a>

#### <a name="upload-to-containers-without-public-access"></a>上傳到沒有公用存取權的容器

1. 將組件上傳到您的儲存體帳戶。 
   在右邊的視窗中，選擇 [上傳]****。

1. 完成上傳之後，為您的組件產生共用存取簽章 (SAS)。 
   從組件的快顯功能表，選取 [取得共用存取簽章]****。

1. 在 [共用存取簽章]**** 窗格中，選取 [產生容器層級共用存取簽章 URI]**** > [建立]****。 
   產生 SAS URL 之後，選擇 [URL]**** 方塊旁的 [複製]****。

1. 返回 Azure 入口網站，其中 [新增組件]**** 窗格已開啟。 
   輸入您的組件名稱。 
   選擇 [大型檔案 (大於 2MB)]****。

   現在會出現 [內容 URI]**** 方塊，而非 [組件]**** 方塊。

1. 在 [內容 URI]**** 方塊中，貼上您先前產生的 SAS URI。 尋找並新增您的組件。

當您的組件完成上傳之後，組件會出現在 [結構描述]**** 清單中。 在您整合帳戶的 [概觀]**** 頁面上的 [元件]**** 下，[組件]**** 圖格現在會顯示已上傳的組件數目。

## <a name="create-maps"></a>建立對應

若要建立可做為對應使用的 XSLT 文件，您可以使用 Visual Studio 2015 來建立 BizTalk Integration 專案，方式是使用 [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)。 在此專案中，您可以建置整合對應檔案，此檔案可讓您以視覺化方式在兩個 XML 結構描述檔案之間對應項目。 建置此專案之後，您將取得 XSLT 文件。
針對整合帳戶中的對應數量限制，請參閱 [Azure Logic Apps 的限制與設定](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)。 

## <a name="add-maps"></a>新增對應

上傳您的對應參考的任何組件之後，您現在可以上傳您的對應。

1. 若您尚未登入，請使用您的 Azure 帳戶認證登入 [Azure 入口網站](https://portal.azure.com)。 

1. 若您的整合帳戶尚未開啟，請在主 Azure 功能表上，選取 [所有服務]****。 
   在搜尋方塊中，輸入「整合帳戶」。 
   選取 [**整合帳戶**]。

   ![尋找整合帳戶](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. 選取要將對應新增到其中的整合帳戶，例如：

   ![選取企業整合帳戶](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. 在您的整合帳戶的 [概觀]**** 頁面上的 [元件]**** 下，選取 [對應]**** 圖格。

   ![選取 [對應]](./media/logic-apps-enterprise-integration-maps/select-maps.png)

1. 在 [對應]**** 頁面開啟之後，選擇 [新增]****。

   ![選擇 [新增]](./media/logic-apps-enterprise-integration-maps/add-map.png)  

<a name="smaller-map"></a>

### <a name="add-maps-up-to-2-mb"></a>新增大小未超過 2 MB 的對應

1. 在 [新增對應]**** 下，輸入您的對應名稱。 

1. 在 [**對應類型**] 底下，選取類型，例如： [**液體**]、[ **xslt**]、[ **xslt 2.0**] 或 [ **xslt 3.0**]。

1. 維持選取 [小型檔案]****。 在 [對應]**** 方塊旁，選擇資料夾圖示。 尋找並選取您要上傳的對應，例如：

   ![上傳對應](./media/logic-apps-enterprise-integration-maps/upload-map-file.png)

   若將 **Name** 屬性留白，在您選取對應檔案之後，對應的檔案名稱會自動出現在該屬性中。 
   不過，您可以使用任何唯一的名稱。

1. 當您就緒時，請選擇 [確定]****。 
   在您的對應檔案完成上傳之後，對應會出現在 [對應]**** 清單中。

   ![已上傳的對應清單](./media/logic-apps-enterprise-integration-maps/uploaded-maps-list.png)

   在您整合帳戶的 [概觀]**** 頁面上的 [元件]**** 下，[對應]**** 圖格現在會顯示已上傳的對應數目，例如：

   ![已上傳的對應](./media/logic-apps-enterprise-integration-maps/uploaded-maps.png)

<a name="larger-map"></a>

### <a name="add-maps-more-than-2-mb"></a>新增大小超過 2 MB 的對應

目前，若要新增較大的對應，請使用 [Azure Logic Apps REST API - 對應](/rest/api/logic/maps/createorupdate)。

<!--

To add larger maps, you can upload your map to 
an Azure blob container in your Azure storage account. 
Your steps for adding maps differ based whether your 
blob container has public read access. So first, check 
whether or not your blob container has public read 
access by following these steps: 
[Set public access level for blob container](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### Check container access level

1. Open Azure Storage Explorer. In the Explorer window, 
   expand your Azure subscription if not already expanded.

1. Expand **Storage Accounts** > {*your-storage-account*} > 
   **Blob Containers**. Select your blob container.

1. From your blob container's shortcut menu, 
   select **Set Public Access Level**.

   * If your blob container has at least public access, choose **Cancel**, 
   and follow these steps later on this page: 
   [Upload to containers with public access](#public-access)

     ![Public access](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * If your blob container doesn't have public access, choose **Cancel**, 
   and follow these steps later on this page: 
   [Upload to containers without public access](#public-access)

     ![No public access](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-maps"></a>

### Add maps to containers with public access

1. Upload the map to your storage account. 
   In the right-hand window, choose **Upload**. 

1. After you finish uploading, select your 
   uploaded map. On the toolbar, choose **Copy URL** 
   so that you copy the map's URL.

1. Return to the Azure portal where the 
   **Add Map** pane is open. Choose **Large file**. 

   The **Content URI** box now appears, 
   rather than the **Map** box.

1. In the **Content URI** box, paste your map's URL. 
   Finish adding your map.

After your map finishes uploading, 
the map appears in the **Maps** list.

<a name="no-public-access-maps"></a>

### Add maps to containers with no public access

1. Upload the map to your storage account. 
   In the right-hand window, choose **Upload**.

1. After you finish uploading, generate a 
   shared access signature (SAS) for your schema. 
   From your map's shortcut menu, 
   select **Get Shared Access Signature**.

1. In the **Shared Access Signature** pane, select 
   **Generate container-level shared access signature URI** > **Create**. 
   After the SAS URL gets generated, next to the **URL** box, choose **Copy**.

1. Return to the Azure portal where the 
   **Add Maps** pane is open. Choose **Large file**.

   The **Content URI** box now appears, 
   rather than the **Map** box.

1. In the **Content URI** box, paste the SAS URI 
   you previously generated. Finish adding your map.

After your map finishes uploading, 
the map appears in the **Maps** list.

-->

## <a name="edit-maps"></a>編輯對應

若要更新現有的對應，您必須上傳包含您想要之變更的新對應檔案。 不過，您可以先下載現有的對應來編輯。

1. 在 [Azure 入口網站](https://portal.azure.com)中，尋找並開啟您的整合帳戶 (若它尚未開啟)。

1. 在主要 Azure 功能表上，選取 [所有服務]****。 在搜尋方塊中，輸入「整合帳戶」。 選取 [**整合帳戶**]。

1. 選取要在其中更新對應的整合帳戶。

1. 在您的整合帳戶的 [概觀]**** 頁面上的 [元件]**** 下，選取 [對應]**** 圖格。

1. 在 [對應]**** 頁面開啟之後，選取您的對應。 
   若要先下載並編輯對應，請選擇 [下載]****，然後儲存對應。

1. 當您準備好上傳已更新的對應時，請在 [對應]**** 頁面上選取您要更新的對應，然後選擇 [更新]****。

1. 尋找並選取您要上傳的已更新對應。 
   在您的對應檔案完成上傳之後，已更新的對應會出現在 [對應]**** 清單中。

## <a name="delete-maps"></a>刪除對應

1. 在 [Azure 入口網站](https://portal.azure.com)中，尋找並開啟您的整合帳戶 (若它尚未開啟)。

1. 在主要 Azure 功能表上，選取 [所有服務]****。 
   在搜尋方塊中，輸入「整合帳戶」。 
   選取 [**整合帳戶**]。

1. 選取要在其中刪除對應的整合帳戶。

1. 在您的整合帳戶的 [概觀]**** 頁面上的 [元件]**** 下，選取 [對應]**** 圖格。

1. 在 [對應]**** 頁面開啟之後，選取您的對應，然後選擇 [刪除]****。

1. 若要確認您要刪除該對應，請選擇 [是]****。

## <a name="next-steps"></a>後續步驟

* [深入了解企業整合套件](../logic-apps/logic-apps-enterprise-integration-overview.md)  
* [深入了解結構描述](../logic-apps/logic-apps-enterprise-integration-schemas.md)
* [深入了解轉換](../logic-apps/logic-apps-enterprise-integration-transform.md)

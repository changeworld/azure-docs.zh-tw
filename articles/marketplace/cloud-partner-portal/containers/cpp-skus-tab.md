---
title: Azure 容器映射的 SKU |Azure 應用商店
description: 設定 Azure 容器的 SKU。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: dsindona
ms.openlocfilehash: adbe3d4c498c8f4e4968ca903f78c34aedca9a4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80279992"
---
# <a name="container-skus-tab"></a>容器 SKU 索引標籤

可使用 [新增供應項目]**** 頁面的 [SKU]**** 索引標籤，建立一個或一個以上的 SKU，並建立 SKU 與新供應項目之間的關聯性。  您可以使用不同的 SKU，依功能集、帳單模式或其他特性來區分解決方案。

## <a name="sku-settings"></a>SKU 設定

開始建立新的供應項目時，沒有任何與該供應項目相關聯的 SKU。 若要建立新的 SKU，請依照下列步驟進行：

1. 在 [SKU] 索引標籤中，選取 [新增 SKU]****

   ![新增 SKU 提示](./media/containers-sku-settings.png)

2. 提供所需的 SKU 和容器資訊。 每個 SKU 都會對應至容器映像。 SKU 有兩個部分：

    -   SKU 中繼資料
    -   容器中繼資料


### <a name="sku-metadata"></a>SKU 中繼資料

SKU 中繼資料包含容器清單的店面顯示資訊。

![SKU 中繼資料](./media/containers-sku-details.png)


### <a name="container-metadata"></a>容器中繼資料

容器中繼資料擁有 Azure Container Registry (ACR) 中映像存放庫詳細資料的參考資訊。 Azure Marketplace 會將此映像複製到 Marketplace 專用的公用登錄中，然後在認證之後提供給客戶使用。 Azure 使用者取用 Azure Marketplace 容器映像的所有要求，都會從 Marketplace 的公用登錄處理，而不是 ACR。

![容器中繼資料](./media/containers-image-repository.png)
    
上一個螢幕捕獲中**的圖像存儲庫詳細資訊**包含以下欄位。  必要欄位由星號 (*) 標示。

-   **訂閱\*ID** - 存在 ACR 的 Azure 訂閱 ID。
-   **資源組名稱\***- ACR 的資源組名稱。
-   **登錄機碼\***- ACR 名稱。
-   **存儲庫名稱\***- 存儲庫名稱。 設定此名稱之後，就無法變更這個值。 使用唯一的名稱避免與您帳戶中的其他供應項目發生衝突。
-   **使用者名\***- 與 ACR 映射關聯的使用者名（管理員使用者名）。
-   **密碼\***- 與 ACR 映射關聯的密碼。

    >[!NOTE]
    >必須提供使用者名稱和密碼，以確保合作夥伴在發佈程序中能存取先前所述的 ACR。


### <a name="image-version"></a>映像版本

發佈容器映像時，您可以提供一個或多個映像標籤以及 SHA 訊息摘要。

**圖像標記\*或摘要**
 
- 此標籤或訊息摘要必須包含 `latest` 標籤和版本標籤 (例如，以 `xx.xx.xx-` 開頭，其中 xx 是數字)， 且應是[資訊清單標籤](https://github.com/estesp/manifest-tool)，以將多個平台設為目標。 也請務必新增資訊清單標籤參考的所有標籤，以便上傳。 
- 您可以使用標籤來新增數個版本的容器。 所有資訊清單標籤 (除了 `latest` 以外) 必須以 `X.Y-` 或 `X.Y.Z-` 開頭，其中 X、Y、Z 是整數。 <br/> 例如，如果 `latest` 標籤指向 `1.0.1-linux-x64`、`1.0.1-linux-arm32`，以及 `1.0.1-windows-arm32`，則需要在此處加入這些標籤。

>[!NOTE]
>請記得將**測試標籤**新增到您的映像，以便您在測試期間識別該映像。


## <a name="next-steps"></a>後續步驟

使用 [Marketplace](./cpp-marketplace-tab.md) 索引標籤建立供應項目的 Marketplace 說明。 

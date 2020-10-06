---
title: 快速入門 - 建立 APIM 執行個體
description: 使用 Azure 入口網站建立新的 APIM 服務執行個體。
author: vladvino
ms.service: api-management
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/08/2020
ms.author: apimpm
ms.openlocfilehash: 66d0874536b358b720b43780d6ecce879ac866ee
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708201"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-the-azure-portal"></a>快速入門：使用 Azure 入口網站建立新的 APIM 服務執行個體

「Azure API 管理 (APIM)」可協助組織將 API 發佈給外部、合作夥伴及內部開發人員，以發揮其資料與服務的潛力。 「API 管理」提供的核心專長認證，透過開發人員參與、商務洞察力、分析、安全性和保護，可確保 API 程式獲致成功。 APIM 可讓您為裝載於任何位置的現有後端服務，建立和管理新式 API 閘道。 如需詳細資訊，請參閱[概觀](api-management-key-concepts.md)。

此快速入門描述使用 Azure 入口網站建立新的 API 管理執行個體的步驟。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

API 管理執行個體

## <a name="sign-in-to-azure"></a>登入 Azure

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="create-a-new-service"></a>建立新的服務

1. 從 Azure 入口網站功能表選取 [建立資源]****。 您也可以在 Azure **首頁**上選取 [建立資源]****。 
   
   選取 [建立資源]:::image type="content" source="media/get-started-create-service-instance/00-CreateResource-01.png" alt-text="":::

   
1. 在 [新增] 頁面上，選取 [整合] > [APIM]。

   Azure API 管理執行個體
   
1. 在 [APIM 服務] 頁面中，輸入設定。

   :::image type="content" source="media/get-started-create-service-instance/get-started-create-service-instance-create-new.png" alt-text="新執行個體":::
   
   | 設定                 | 說明：   |                                                                     
   |-------------------------|-----------------------------------------------|
   | name - **                | APIM 服務的唯一名稱。 此名稱無法在之後變更。 服務名稱會參考服務和對應的 Azure 資源。 <br/> 服務名稱會用來產生預設網域名稱： *\<name\>.azure-api.net。* 如果您想要使用自訂網域名稱，請參閱[設定自訂網域](configure-custom-domain.md)。 |
   | **--subscription**：          | 將在其下建立這個新服務執行個體的訂用帳戶。   |
   | **資源群組**      |  選取新的或現有的資源群組。 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 |
   | **位置**          | 從可用的 APIM 服務位置選取您附近的地理區域。 | 
   | **組織名稱**   | 您的組織名稱。 此名稱會在有許多地方使用，包括開發人員入口網站的標題和通知電子郵件的傳送者。 |                                                         
   | **管理員電子郵件** | 此電子郵件地址會接收所有來自 **APIM** 的通知。   |  
   | **定價層**        | 選取 [開發人員] 層來評估服務。 此階層不適用於生產環境。 如需有關調整 API 管理層規模的詳細資訊，請參閱[升級和調整規模](upgrade-and-scale.md)。 |

3. 選取 [建立]****。

    > [!TIP]
    > 這可能需要 30 到 40 分鐘的時間，才能在此階層中建立及啟用 APIM 服務。 選取 [釘選到儀表板]**** 以更輕鬆找到新建立的服務。

[!INCLUDE [api-management-navigate-to-instance](../../includes/api-management-navigate-to-instance.md)]

在 [概觀] 頁面上，檢閱服務的屬性。

   API 管理執行個體

當您的 APIM 服務執行個體上線時，您就可以開始使用了。 從[匯入和發佈第一個 API](import-and-publish.md) 教學課程開始。

## <a name="clean-up-resources"></a>清除資源

若不再需要，您可以遵循下列步驟來移除資源群組和所有相關資源：

1. 在 Azure 入口網站中，搜尋並選取 [資源群組]****。 您也可以在**首頁**上選取 [資源群組]****。 

   :::image type="content" source="media/get-started-create-service-instance/00-DeleteResource-01.png" alt-text="新執行個體":::

1. 在 [資源群組]**** 頁面中，選取您的資源群組。

   選取 [資源群組]:::image type="content" source="media/get-started-create-service-instance/00-DeleteResource-02.png" alt-text="":::。

1. 在 [資源群組] 頁面中，選取 [刪除資源群組]****。 
   
1. 輸入您的資源群組名稱，然後選取 [刪除]****。

   刪除資源群組

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [匯入和發佈您的第一個 API](import-and-publish.md)

---
title: 在 Azure API 管理中匯入和發佈您的第一個 API
description: 了解如何將 OpenAPI 規格 API 匯入至 Azure API 管理，並在 Azure 入口網站中測試您的 API。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: apimpm
ms.openlocfilehash: 886063dcf886d79ac960814f20b3789e8e3b6839
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2020
ms.locfileid: "78163472"
---
# <a name="import-and-publish-your-first-api"></a>匯入和發佈您的第一個 API 

本教學課程說明如何以 JSON 格式將 OpenAPI 規格後端 API 匯入至 Azure API 管理。 Microsoft 會提供後端 API，並將其裝載在 Azure 上 ([https://conferenceapi.azurewebsites.net?format=json](https://conferenceapi.azurewebsites.net?format=json))。

當您將後端 API 匯入至 API 管理後，API 管理 API 就會成為後端 API 的外觀。 您可以依需求在 API 管理中自訂外觀，而無須存取後端 API。 如需詳細資訊，請參閱[轉換及保護您的 API](transform-api.md)。 

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 將 API 匯入至 API 管理
> * 在 Azure 入口網站中測試 API

![新增 API](./media/api-management-import-and-publish/created-api.png)

## <a name="prerequisites"></a>Prerequisites

- 了解 [Azure API 管理術語](api-management-terminology.md)。
- [建立 Azure APIM 執行個體](get-started-create-service-instance.md)。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>匯入和發佈後端 API

本節示範如何匯入和發佈 OpenAPI 規格後端 API。
 
1. 在 API 管理執行個體的左側導覽中，從 [API 管理]  區段中選取 [API]  。
1. 選取 [OpenAPI]  磚，然後在快顯畫面上選取 [完整]  。
1. 在 [從 OpenAPI 規格建立]  畫面上，使用下表中的值建立您的 API。
   
   表單上的欄位旁若有紅色星號，表示此為必要欄位。 您可以在建立期間或稍後前往 [設定]  索引標籤設定 API 值。 
   
   ![建立 API](./media/api-management-import-and-publish/create-api.png)
   
   |設定|值|描述|
   |-------|-----|-----------|
   |**OpenAPI 規格**|*https:\//conferenceapi.azurewebsites.net?format=json*|實作 API 的服務。 API 管理則將要求轉送至此位址。|
   |**顯示名稱**|在您輸入先前的服務 URL 後，API 管理會根據 JSON 填入此欄位。|名稱會顯示於開發人員入口網站中。|
   |**名稱**|在您輸入先前的服務 URL 後，API 管理會根據 JSON 填入此欄位。|API 的唯一名稱。|
   |**說明**|在您輸入先前的服務 URL 後，API 管理會根據 JSON 填入此欄位。|API 的選擇性描述。|
   |**URL 配置**|**HTTPS**|可使用哪些通訊協定來存取 API。|
   |**API URL 尾碼**|*conference*|尾碼會附加至 API 管理服務的基底 URL。 API 管理會依尾碼來區分 API，因此，特定發行者的每個 API 都必須有唯一的尾碼。|
   |**產品**|**無限制**|一或多個 API 的關聯。 每個 API 管理執行個體會隨附兩個範例產品：[入門]  和 [無限制]  。 您可以將 API 與產品 (在此範例中為 [無限制]  ) 產生關聯，以發佈 API。<br/>您可以將數個 API 納入產品中，並透過開發人員入口網站將其提供給開發人員。 若要將此 API 新增至另一個產品，請輸入或選取產品名稱。 重複此步驟，將 API 新增至多個產品。 您稍後也可以從 [設定]  頁面，將 API 新增至產品。<br/>為了取得 API 的存取權，開發人員必須先訂閱產品。 當他們訂閱時，就能取得適用於該產品中任何 API 適用的訂用帳戶金鑰。 <br/>如果您建立了 APIM 執行個體，您就已經是系統管理員，因此您已訂閱執行個體中的每個產品。|
   |**Tags** (標籤)| |標籤可用來組織用於搜尋、分組或篩選的 API。|
   |**要為此 API 設定版本嗎?**|選取或取消選取|如需版本設定的詳細資訊，請參閱[發佈多個 API 版本](api-management-get-started-publish-versions.md)。|
   
   > [!NOTE]
   > 若要發佈 API，您必須將它與產品產生關連。 您可以從 [設定]  頁面執行此作業。
   
1. 選取 [建立]  。

如果您在匯入 API 定義時發生問題，請參閱[已知問題和限制的清單](api-management-api-import-restrictions.md)。

## <a name="test-the-new-api-in-the-azure-portal"></a>在 Azure 入口網站中測試新的 API

您可以直接從 Azure 入口網站呼叫 API 作業，此處可讓您以便利的方式檢視和測試作業。

1. 在 API 管理執行個體的左側導覽中，從 [API 管理]  區段中選取 [API]  ，然後選取 [示範會議 API]  。
1. 選取 [測試]  索引標籤，然後選取 [Getspeakers]  。 此頁面會顯示 [查詢參數]  和 [標頭]  (如果有的話)。 針對與此 API 相關聯的訂用帳戶金鑰，會自動填入 **Ocp-Apim-Subscription-Key**。
1. 選取 [傳送]  。
   
   ![測試 API 對應](./media/api-management-import-and-publish/01-import-first-api-01.png)
   
   後端會回應 **200 確定**與部分資料。

## <a name="next-steps"> </a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 匯入第一個 API
> * 在 Azure 入口網站中測試 API

繼續進行下一個教學課程，以了解如何建立和發佈產品：

> [!div class="nextstepaction"]
> [建立和發佈產品](api-management-howto-add-products.md)

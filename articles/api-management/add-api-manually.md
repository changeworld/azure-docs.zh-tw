---
title: 使用 Azure 入口網站手動新增 API | Microsoft Docs
description: 本教學課程示範如何使用 API 管理 (APIM) 來手動新增 API。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: apimpm
ms.openlocfilehash: 39a3b9d7dd9efbda93de0b5d7c5f9938922d0012
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91631285"
---
# <a name="add-an-api-manually"></a>手動新增 API

本文中的步驟會示範如何使用 Azure 入口網站，來將 API 手動新增至 API 管理 (APIM) 執行個體。 常見的情況是，您會想在模擬 API 時建立空白的 API 並手動進行定義。 如需有關模擬 API 的詳細資訊，請參閱[模擬 API 回應](mock-api-responses.md)。

如果您想要匯入現有的 API，請參閱[相關主題](#related-topics)一節。

在本文中，我們會建立空白的 API，並指定 [httpbin.org](https://httpbin.org) (公用測試服務) 作為後端 API。

## <a name="prerequisites"></a>Prerequisites

完成下列快速入門：[建立 Azure API 管理執行個體](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-an-api"></a>建立 API

1. 在 Azure 入口網站中，瀏覽至您的 APIM 服務，然後從功能表中選取 [API]  。
2. 從左側功能表中選取 [+ 新增 API]  。
3. 從清單選取 [空白 API]  。  
    ![空白 API](media/add-api-manually/blank-api.png)  
4. 輸入 API 的設定。 這些設定會在 [匯入和發佈您的第一個 API](import-and-publish.md#import-and-publish-a-backend-api) 教學課程中說明。
5. 選取 [建立]  。

此時，APIM 中沒有對應至後端 API 中任何作業的作業。 如果您呼叫透過後端公開的作業，而不是呼叫透過 APIM 公開的作業，您會收到 **404**。

>[!NOTE] 
> 依預設，當您新增 API 時 (即使 API 已連線到某些後端服務)，APIM 不會公開任何作業，直到您允許為止。 若要允許您的後端服務作業，請建立對應至後端作業的 APIM 作業。

## <a name="add-and-test-an-operation"></a>新增和測試作業

本節會示範如何新增 "/get" 作業，以便將其對應至後端 "http://httpbin.org/get" 作業。

### <a name="add-an-operation"></a>新增作業

1. 選取您在上一個步驟中建立的 API。
2. 按一下 [+ 新增作業]  。
3. 在 **URL** 中選取 **GET**，並在資源中輸入 " */get*"。
4. 輸入 "*FetchData* 作為**顯示名稱**。
5. 選取 [儲存]  。

### <a name="test-an-operation"></a>測試作業

在 Azure 入口網站中測試作業。 或者，您可以在**開發人員入口網站**中測試作業。

1. 選取 [測試]  索引標籤。
2. 選取 **FetchData**。
3. 按 [傳送]  。

此時會出現 "http://httpbin.org/get" 作業產生的回應。 如果您想要轉換您的作業，請參閱[轉換及保護您的 API](transform-api.md)。

## <a name="add-and-test-a-parameterized-operation"></a>新增和測試參數化作業

本節會示範如何新增採用參數的作業。 在此案例中，我們將作業對應至 "http://httpbin.org/status/200"。

### <a name="add-the-operation"></a>新增作業

1. 選取您在上一個步驟中建立的 API。
2. 按一下 [+ 新增作業]  。
3. 在 **URL** 中選取 **GET**，並在資源中輸入" */status/{code}* "。 您可以選擇性地提供與此參數相關聯的一些資訊。 例如，在 [類型]  中輸入「數字」  ，在 [值]  中輸入「200」  (預設)。
4. 輸入 "GetStatus" 作為**顯示名稱**。
5. 選取 [儲存]  。

### <a name="test-the-operation"></a>測試作業 

在 Azure 入口網站中測試作業。  或者，您可以在**開發人員入口網站**中測試作業。

1. 選取 [測試]  索引標籤。
2. 選取 **GetStatus**。 依預設，此字碼值會設定為 "200  "。 您可以加以變更以測試其他值。 例如，輸入 "418  "。
3. 按 [傳送]  。

    此時會出現 "http://httpbin.org/status/200" 作業產生的回應。 如果您想要轉換您的作業，請參閱[轉換及保護您的 API](transform-api.md)。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [轉換及保護已發佈的 API](transform-api.md)

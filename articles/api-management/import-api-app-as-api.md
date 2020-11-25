---
title: 使用 Azure 入口網站匯入 API 應用程式作為 API | Microsoft Docs
description: 本文示範如何使用 API 管理 (APIM) 來匯入 API 應用程式作為 API。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/22/2020
ms.author: apimpm
ms.openlocfilehash: 41209233ec59f578db4ff7fd344bb96aefeb975e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994735"
---
# <a name="import-an-api-app-as-an-api"></a>匯入 API 應用程式作為 API

本文示範如何匯入 API 應用程式作為 API。 本文也會示範如何測試 APIM API。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 匯入 API 應用程式作為 API
> * 在 Azure 入口網站中測試 API
> * 在開發人員入口網站中測試 API

## <a name="prerequisites"></a>Prerequisites

+ 完成下列快速入門：[建立 Azure API 管理執行個體](get-started-create-service-instance.md)
+ 確定您的訂用帳戶中有 API 應用程式。 如需詳細資訊，請參閱 [App Service 文件](../app-service/index.yml)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>匯入和發佈後端 API

1. 在 Azure 入口網站中，瀏覽至您的 APIM 服務，然後從功能表中選取 [API]  。
2. 從 [加入新的 API]  清單選取 [API 應用程式]  。

    ![API 應用程式](./media/import-api-app-as-api/api-app.png)
3. 按 [瀏覽]  以查看您訂用帳戶中的 API Apps 清單。
4. 選取應用程式。 APIM 會尋找與所選取應用程式相關聯的 Swagger，擷取並匯入它。 

    萬一 APIM 找不到 Swagger，它會公開 API 以作為「穿通」API。 
5. 新增 API URL 尾碼。 此尾碼是用來在這個 APIM 執行個體中識別此特定 API 的名稱。 它在這個 APIM 執行個體中必須是唯一的。
6. 透過將 API 關聯至某個產品來發佈 API。 在本案例中，我們使用「無限制」  的產品。  如果您想要發佈 API 以供開發人員使用，請將它新增至產品。 您可以在 API 建立期間執行此動作，或稍後設定它。

    產品是一或多個 API 的關聯。 您可以包括數個 API，並透過開發人員入口網站將它們提供給開發人員。 開發人員必須先訂閱產品，才能取得 API 的存取權。 當他們訂閱時，就能取得適用於該產品中任何 API 的中訂用帳戶金鑰。 如果您建立了 APIM 執行個體，您就已經是系統管理員，因此根據預設，您已訂閱每一個產品。

    依預設，每個 API 管理執行個體會隨附兩個範例產品：

    * **入門**
    * **無限制**   
7. 輸入其他 API 設定。 您可以在建立期間設定這些值，或稍後前往 [設定]  索引標籤來進行設定。這些設定會在[匯入和發佈您的第一個 API](import-and-publish.md#import-and-publish-a-backend-api) 教學課程中說明。
8. 選取 [建立]  。

## <a name="test-the-new-api-in-the-azure-portal"></a>在 Azure 入口網站中測試新的 API

您可以從 Azure 入口網站直接呼叫作業，以便檢視和測試 API 的作業。  

1. 選取您在上一個步驟中建立的 API。
2. 按 [測試]  索引標籤。
3. 選取某個作業。

    頁面會顯示查詢參數的欄位和標頭的欄位。 其中一個標頭是 "Ocp-Apim-Subscription-Key"，它適用於與此 API 相關聯之產品的訂用帳戶金鑰。 如果您建立了 APIM 執行個體，您就已經是系統管理員，因此會自動填入此金鑰。 
1. 按 [傳送]  。

    後端會回應 **200 確定** 與部分資料。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [轉換及保護已發佈的 API](transform-api.md)

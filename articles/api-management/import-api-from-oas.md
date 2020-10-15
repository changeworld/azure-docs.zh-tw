---
title: 使用 Azure 入口網站匯入 OpenAPI 規格 | Microsoft Docs
description: 了解如何使用 API 管理匯入 OpenAPI，然後在 Azure 和開發人員入口網站中測試 API。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: apimpm
ms.openlocfilehash: c498046b9088f78eda95693fa23f0a0bb3472724
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91626865"
---
# <a name="import-an-openapi-specification"></a>匯入 OpenAPI 規格

本文示範如何匯入位於 https://conferenceapi.azurewebsites.net?format=json 的「OpenAPI 規格」後端 API。 這個後端 API 是由 Microsoft 所提供且裝載於 Azure 上。 本文也會示範如何測試 APIM API。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 匯入「OpenAPI 規格」後端 API
> * 在 Azure 入口網站中測試 API
> * 在開發人員入口網站中測試 API

## <a name="prerequisites"></a>Prerequisites

完成下列快速入門：[建立 Azure API 管理執行個體](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>匯入和發佈後端 API

1. 在 Azure 入口網站中，瀏覽至您的 APIM 服務，然後從功能表中選取 [API]  。
2. 從 [加入新的 API]  清單選取 [OpenAPI 規格]  。

    ![OpenAPI 規格](./media/import-api-from-oas/oas-api.png)
3. 輸入 API 設定。 您可以在建立期間設定這些值，或稍後前往 [設定]  索引標籤來進行設定。這些設定會在[匯入和發佈您的第一個 API](import-and-publish.md#import-and-publish-a-backend-api) 教學課程中說明。
4. 選取 [建立]  。

> [!NOTE]
> API 匯入限制記載於[另一篇文章](api-management-api-import-restrictions.md)。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [轉換及保護已發佈的 API](transform-api.md)

---
title: Azure 靜態 Web Apps 搭配 Azure Functions 時的 API 支援
description: 了解 Azure 靜態 Web Apps 支援的 API 功能
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: f5f40a615bc5faab6265f42d0728403e2735aa0f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84791617"
---
# <a name="api-support-in-azure-static-web-apps-preview-with-azure-functions"></a>Azure 靜態 Web Apps 預覽版搭配 Azure Functions 時的 API 支援

Azure 靜態 Web Apps 可透過 [Azure Functions](../azure-functions/functions-overview.md) 提供無伺服器 API 端點。 藉由運用 Azure Functions，API 可根據需求動態調整，並且包含下列功能：

- **整合式安全性**，可直接存取使用者[驗證和角色型授權](user-information.md)資料。
- **順暢的路由**，可讓 _api_ 路由安全地供 Web 應用程式使用，而不需要自訂 CORS 規則。
- **Azure Functions** v3，與 Node.js 12 相容。
- **HTTP 觸發程序**和輸出繫結。

## <a name="configuration"></a>組態

API 端點可透過 _api_ 路由供 Web 應用程式使用。 雖然此路由是固定的，但您可以控制相關聯的 Azure Functions 應用程式所在的資料夾。 您可以編輯在存放庫中位於 _.github/workflows_ 資料夾內的[工作流程 YAML 檔案](github-actions-workflow.md#build-and-deploy)，以變更此位置。

## <a name="constraints"></a>條件約束

Azure 靜態 Web Apps 可透過 Azure Functions 提供 API。 Azure Functions 的功能著重於一組特定的功能，可讓您建立 Web 應用程式的 API，並且讓 Web 應用程式能安全地連線至 API。 這些功能附帶一些條件約束，包括：

- API 路由前置詞必須是 _api_。
- API 函數應用程式必須是 JavaScript。
- API 函式的路由規則僅支援[使用角色的](routes.md#securing-routes-with-roles)重新[導向](routes.md#redirects)和保護路由。
- 觸發程序和繫結限定為 [HTTP](../azure-functions/functions-bindings-http-webhook.md)。
  - 除了輸出繫結以外，所有其他的 [Azure Functions 觸發程序和繫結](../azure-functions/functions-triggers-bindings.md#supported-bindings)都會受到限制。
- 必須將 [Application Insights](../azure-functions/functions-monitoring.md) 新增至函式應用程式，才能使用記錄。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [新增 API](add-api.md)

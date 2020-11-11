---
title: 使用 Azure 管理用戶端程式庫建立認知服務資源
titleSuffix: Azure Cognitive Services
description: 使用 Azure 管理用戶端程式庫建立和管理 Azure 認知服務資源。
services: cognitive-services
keywords: 認知服務, 認知智慧, 認知解決方案, ai 服務
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 09/14/2020
ms.author: pafarley
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: 1fd748a0184c1718ac9450aaca3e2db1a185051a
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2020
ms.locfileid: "94368945"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-management-client-library"></a>快速入門：使用 Azure 管理用戶端程式庫建立認知服務資源

使用快速入門，以 Azure 管理用戶端程式庫建立和管理 Azure 認知服務資源。

Azure 認知服務是可搭配 REST API 和用戶端程式庫 SDK 的雲端式服務，可協助開發人員建置認知智慧應用程式，且無須直接人工智慧 (AI) 或資料科學技術或知識。 Azure 認知服務可讓開發人員使用認知解決方案，輕鬆地將認知功能新增至其應用程式，以查看、聆聽、說出、了解，甚至是開始的原因。

個別的 AI 服務會由您在 Azure 訂用帳戶下建立的 Azure [資源](../azure-resource-manager/management/manage-resources-portal.md)來表示。 建立資源之後，您可以使用產生的金鑰和端點來驗證您的應用程式。

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK quickstart](includes/quickstarts/management-csharp.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java SDK quickstart](includes/quickstarts/management-java.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK quickstart](includes/quickstarts/management-node.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK quickstart](includes/quickstarts/management-python.md)]

::: zone-end
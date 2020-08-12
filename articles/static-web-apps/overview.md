---
title: 什麼是 Azure Static Web Apps？
description: Azure Static Web Apps 的主要功能。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 47140bccc8c2a1b69f083502d031a9db6d21294e
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87835777"
---
# <a name="what-is-azure-static-web-apps-preview"></a>什麼是 Azure Static Web Apps 預覽？

Azure Static Web Apps 是一種服務，會自動從 GitHub 存放庫建置完整堆疊 Web 應用程式，並且部署至 Azure。

:::image type="content" source="media/overview/static-apps-overview.png" alt-text="Static Web Apps 概觀":::

Azure Static Web Apps 的工作流程是專為開發人員每天的工作流程量身打造的。 應用程式是根據 GitHub 互動來建置和部署。

當您建立 Azure Static Web Apps 資源時，Azure 會在應用程式的原始程式碼存放庫中設定 GitHub Actions 工作流程，監視您選擇的分支。 每次您對受監視的分支推送認可或接受提取要求時，GitHub Actions 會自動建置您的應用程式及其 API，並將其部署至 Azure。

靜態 Web 應用程式通常是使用程式庫和架構 (例如 Angular、React、Svelte 或 Vue) 來建置。 這些應用程式包含 HTML、CSS、JavaScript 和組成應用程式的映像資產。 使用傳統的 Web 伺服器，這些資產會與任何必要的 API 端點一起從單一伺服器提供。

使用 Static Web Apps，靜態資產會與傳統的 Web 伺服器分開，並且是從分散於世界各地的點來提供。 這種散發方式可讓檔案的處理速度更快，因為檔案實際上較接近使用者。 此外，API 端點是使用[無伺服器架構](../azure-functions/functions-overview.md)來裝載，這可避免將完整後端伺服器全部整合在一起的需求。

## <a name="key-features"></a>主要功能

- **Web 裝載**，適用於像是 HTML、CSS、JavaScript 與影像等靜態內容。
- **整合式 API**，受 Azure Functions 支援。
- **第一方 GitHub 整合**，其中存放庫變更會觸發建置和部署。
- **全域散發**靜態內容，將內容放在更接近使用者的範圍。
- **免費的 SSL 憑證**，會自動更新。
- **自訂網域**\*，為您的應用程式提供品牌的自訂。
- **無縫安全性模型**，會在呼叫 API 時使用反向 Proxy，這不需要 CORS 設定。
- **驗證提供者整合**，有 Azure Active Directory、Facebook、Google、GitHub 和 Twitter。
- **可自訂的授權角色定義**和指派。
- **後端路由規則**能夠完全控制您所提供的內容和路由。
- **產生的暫存版本**，由提取要求提供支援，讓您的網站在發佈前先啟用預覽版本。

## <a name="what-you-can-do-with-static-web-apps"></a>如何使用 Static Web Apps

- **建置新式 JavaScript 應用程式**，使用像是 [Angular](getting-started.md#tabpanel_CeZOj-G++Q_angular)、[React](getting-started.md#tabpanel_CeZOj-G++Q_react)、[Svelte](https://docs.microsoft.com/learn/modules/publish-app-service-static-web-app-api/)、[Vue](getting-started.md#tabpanel_CeZOj-G++Q_vue) 的架構和程式庫，具有 [Azure Functions](apis.md) 後端。
- **發佈靜態網站**，使用像是 [Gatsby](publish-gatsby.md)、[Hugo](publish-hugo.md)、[VuePress](publish-vuepress.md) 的架構。
- **部署 Web 應用程式**，使用像是 [Next.js](deploy-nextjs.md) 和 [Nuxt.js](deploy-nuxtjs.md) 的架構。

\* 預覽期間不支援 Apex 網域註冊。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建置您的第一個靜態應用程式](getting-started.md)

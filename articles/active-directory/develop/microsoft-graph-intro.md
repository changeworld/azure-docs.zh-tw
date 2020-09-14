---
title: Microsoft Graph API
description: Microsoft Graph API 是一種 RESTful web API，可讓您存取 Microsoft 雲端服務資源。
author: davidmu1
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 02/13/2020
ms.author: davidmu
ms.custom: aaddev
ms.openlocfilehash: 13cbeca909b445d75fcbcf7b3751183421479d42
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2020
ms.locfileid: "90052557"
---
# <a name="microsoft-graph-api"></a>Microsoft Graph API

Microsoft Graph API 是一種 RESTful web API，可讓您存取 Microsoft 雲端服務資源。 註冊您的應用程式並取得使用者或服務的驗證權杖之後，您可以對 Microsoft Graph API 提出要求。 如需詳細資訊，請參閱 [Microsoft Graph 的總覽](/graph/overview)。

Microsoft Graph 會公開 REST Api 和用戶端程式庫，以存取下列 Microsoft 365 服務的資料：
- Microsoft 365 services： Delve、Excel、Microsoft 預約、Microsoft 小組、OneDrive、OneNote、Outlook/Exchange、Planner 和 SharePoint
- 企業行動力和安全性服務：先進的威脅分析、先進的威脅防護、Azure Active Directory、身分識別管理員和 Intune
- Windows 10 服務：活動、裝置、通知
- Dynamics 365 Business Central

## <a name="versions"></a>版本

Microsoft Graph 目前支援兩種版本：1.0 版和 Beta 版。 1.0 版包含正式推出的 Api。 使用 v1.0 版的所有生產環境應用程式。 Beta 版包含目前處於預覽狀態的 Api。 由於我們可能會對搶鮮版（Beta） Api 引進重大變更，因此建議您只使用 Beta 版來測試開發中的應用程式;請勿在生產應用程式中使用搶鮮版（Beta） Api。 如需詳細資訊，請參閱 [Microsoft Graph 的版本控制、支援和中斷變更原則](/graph/versioning-and-support)。

若要開始使用搶鮮版（Beta） Api，請參閱 [Microsoft Graph Beta 端點參考](/graph/api/overview?view=graph-rest-beta)

若要開始使用 v1.0 Api，請參閱 [Microsoft Graph REST API v1.0 參考](/graph/api/overview?view=graph-rest-1.0)

## <a name="get-started"></a>開始使用

若要讀取或寫入資源（例如使用者或電子郵件訊息），您必須建立如下所示的要求：

`{HTTP method} https://graph.microsoft.com/{version}/{resource}?{query-parameters}`

如需有關所建立要求之元素的詳細資訊，請參閱 [使用 MICROSOFT GRAPH API](/graph/use-the-api)

您可以使用快速入門範例來示範如何存取 Microsoft Graph API 的威力。 可用的範例可透過一個驗證存取兩項服務： Microsoft 帳戶和 Outlook。 每個快速入門都會從 Microsoft 帳戶使用者的設定檔存取訊號，並顯示其行事曆中的事件。
快速入門包含四個步驟：
- 選取您的平臺
- 取得 (用戶端識別碼) 的應用程式識別碼
- 建置範例
- 登入並查看您行事曆上的事件

當您完成快速入門時，您的應用程式已準備好執行。 如需詳細資訊，請參閱 [Microsoft Graph 快速入門常見問題](/graph/quick-start-faq)。 若要開始使用範例，請參閱 [Microsoft Graph 快速入門](https://developer.microsoft.com/graph/quick-start)。

## <a name="tools"></a>工具

Microsoft Graph Explorer 是一種 web 工具，可用來建立及測試使用 Microsoft Graph Api 的要求。 您可以在下列位置存取 Microsoft Graph Explorer： `https://developer.microsoft.com/graph/graph-explorer` 。

Postman 是一種工具，您也可以使用 Microsoft Graph Api 來建立和測試要求。 您可以在下列位置下載 Postman： `https://www.getpostman.com/` 。 若要與 Postman 中的 Microsoft Graph 互動，您可以使用 Postman 中的 Microsoft Graph 集合。 如需詳細資訊，請參閱 [使用 Postman 搭配 MICROSOFT GRAPH API](/graph/use-postman?context=graph%2Fapi%2Fbeta&view=graph-rest-beta)。
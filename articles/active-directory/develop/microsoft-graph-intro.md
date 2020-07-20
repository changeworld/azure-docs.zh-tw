---
title: Microsoft Graph API
description: Microsoft Graph API 是一個 RESTful 的 Web API，可讓您存取 Microsoft Cloud 服務資源。
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
ms.openlocfilehash: 0cdcb5287434c72bf54337611d67de8d6f65d8d9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85479507"
---
# <a name="microsoft-graph-api"></a>Microsoft Graph API

Microsoft Graph API 是一個 RESTful 的 Web API，可讓您存取 Microsoft Cloud 服務資源。 註冊應用程式並取得使用者或服務的驗證權杖之後，您就可以對 Microsoft Graph API 提出要求。 如需詳細資訊，請參閱[Microsoft Graph 的總覽](https://docs.microsoft.com/graph/overview)。

Microsoft Graph 會公開 REST Api 和用戶端程式庫，以存取下列 Microsoft 365 服務上的資料：
- Office 365 服務： Delve、Excel、Microsoft 預約、Microsoft 團隊、OneDrive、OneNote、Outlook/Exchange、Planner 和 SharePoint
- 企業行動力與安全性服務：先進的威脅分析、先進的威脅防護、Azure Active Directory、Identity Manager 和 Intune
- Windows 10 服務：活動、裝置、通知
- Dynamics 365 Business Central

## <a name="versions"></a>版本

Microsoft Graph 目前支援兩種版本： v1.0 和搶鮮版（Beta）。 V1.0 版本包含正式推出的 Api。 針對所有生產環境應用程式使用 v1.0 版。 搶鮮版（Beta）包含目前處於預覽狀態的 Api。 因為我們可能會引進搶鮮版（Beta） Api 的重大變更，所以我們建議您只使用搶鮮版（Beta）版本來測試開發中的應用程式;請勿在您的生產應用程式中使用搶鮮版（Beta） Api。 如需詳細資訊，請參閱[Microsoft Graph 的版本控制、支援和重大變更原則](https://docs.microsoft.com/graph/versioning-and-support)。

若要開始使用搶鮮版（Beta） Api，請參閱[Microsoft Graph Beta 版端點參考](https://docs.microsoft.com/graph/api/overview?view=graph-rest-beta)

若要開始使用 v1.0 Api，請參閱[Microsoft Graph REST API v1.0 參考](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)

## <a name="get-started"></a>開始使用

若要讀取或寫入資源（例如使用者或電子郵件訊息），您可以建立如下所示的要求：

`{HTTP method} https://graph.microsoft.com/{version}/{resource}?{query-parameters}`

如需有關已建立的要求之元素的詳細資訊，請參閱[使用 MICROSOFT GRAPH API](https://docs.microsoft.com/graph/use-the-api)

您可以使用快速入門範例來示範如何存取 Microsoft Graph API 的威力。 可用的範例可使用一種驗證來存取兩項服務： Microsoft 帳戶和 Outlook。 每個快速入門會從 Microsoft 帳戶使用者的設定檔存取訊號，並顯示其行事曆中的事件。
快速入門包含四個步驟：
- 選取您的平臺
- 取得您的應用程式識別碼（用戶端識別碼）
- 建置範例
- 登入並查看行事曆上的事件

當您完成快速入門時，您會有一個已準備好要執行的應用程式。 如需詳細資訊，請參閱[Microsoft Graph 快速入門常見問題](https://docs.microsoft.com/graph/quick-start-faq)。 若要開始使用範例，請參閱[Microsoft Graph 快速入門](https://developer.microsoft.com/graph/quick-start)。

## <a name="tools"></a>工具

Microsoft Graph Explorer 是一個 web 型工具，可讓您使用 Microsoft Graph Api 來建立及測試要求。 您可以存取 Microsoft Graph Explorer，網址為： `https://developer.microsoft.com/graph/graph-explorer` 。

Postman 是一種工具，您也可以使用 Microsoft Graph Api 來建立和測試要求。 您可以下載 Postman，網址為： `https://www.getpostman.com/` 。 若要與 Postman 中的 Microsoft Graph 互動，您可以使用 Postman 中的 Microsoft Graph 集合。 如需詳細資訊，請參閱搭配[使用 Postman 與 MICROSOFT GRAPH API](/graph/use-postman?context=graph%2Fapi%2Fbeta&view=graph-rest-beta)。

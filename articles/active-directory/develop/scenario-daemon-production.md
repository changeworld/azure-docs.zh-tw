---
title: 將呼叫 web Api 的 daemon 應用程式移至生產-Microsoft 身分識別平臺 |蔚藍
description: 瞭解如何將呼叫 web Api 的 daemon 應用程式移至生產環境
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 72e6d0a8677f7a8175223b80541c99026a9eb16f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88119159"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>呼叫 web Api 的 Daemon 應用程式-移至生產環境

既然您已瞭解如何取得及使用權杖來進行服務對服務呼叫，請瞭解如何將您的應用程式移至生產環境。

## <a name="deployment---multitenant-daemon-apps"></a>部署-多租使用者 daemon 應用程式

如果您是建立可在數個租使用者中執行的背景程式應用程式的 ISV，您需要確定租使用者系統管理員：

- 布建應用程式的服務主體。
- 將同意授與應用程式。

您必須向客戶說明如何執行這些作業。 如需詳細資訊，請參閱 [要求整個租使用者的同意](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>後續步驟

以下是一些可協助您深入瞭解的連結：

# <a name="net"></a>[.NET](#tab/dotnet)

- 快速入門： [使用應用程式的身分識別取得權杖，並從主控台應用程式呼叫 MICROSOFT GRAPH API](./quickstart-v2-netcore-daemon.md)。
- 參考檔：
  - 具現化 [>confidentialclientapplication](/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)。
  - 呼叫 [AcquireTokenForClient](/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder)。
- 其他範例/教學課程：
  - [microsoft-身分識別平臺-主控台-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) 具有簡單的 .net Core daemon 主控台應用程式，可顯示租使用者查詢 Microsoft Graph 的使用者。

    ![範例 daemon 應用程式拓撲](media/scenario-daemon-app/daemon-app-sample.svg)

    相同的範例也說明了憑證的變化：

    ![範例 daemon 應用程式拓撲-憑證](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [webapp-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) 具有 ASP.NET 的 MVC web 應用程式，可使用應用程式的身分識別（而不是代表使用者）來同步處理 Microsoft Graph 的資料。 此範例也會說明系統管理員同意流程。

    ![拓撲](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="python"></a>[Python](#tab/python)

試用快速入門以 [取得權杖，並使用應用程式的身分識別，從 Python 主控台應用程式呼叫 MICROSOFT GRAPH API](./quickstart-v2-python-daemon.md)。

# <a name="java"></a>[Java](#tab/java)

MSAL JAVA 目前處於公開預覽狀態。 如需詳細資訊，請參閱 [MSAL JAVA 開發人員範例](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples)。

---
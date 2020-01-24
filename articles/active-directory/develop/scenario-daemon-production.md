---
title: 將背景工作應用程式呼叫 web Api 移至生產環境-Microsoft 身分識別平臺 |Azure
description: 瞭解如何將會呼叫 web Api 的 daemon 應用程式移至生產環境
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 990273e84bfceb9f4a19eae8bf5890e8303a5857
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702261"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>呼叫 web Api 的 Daemon 應用程式-移至生產環境

現在您已瞭解如何取得並使用權杖來進行服務對服務呼叫，請瞭解如何將您的應用程式移至生產環境。

## <a name="deployment---case-of-multi-tenant-daemon-apps"></a>部署-多租使用者 daemon 應用程式的案例

如果您是 ISV，而建立可在數個租使用者中執行的 daemon 應用程式，您將需要確定租使用者系統管理員：

- 布建應用程式的服務主體
- 將同意授與應用程式

您必須向客戶說明如何執行這些作業。 如需詳細資訊，請參閱[要求對整個租使用者的同意](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>後續步驟

以下是一些深入瞭解的連結：

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

- 如果您尚未這麼做，請嘗試快速入門[取得權杖，並使用應用程式的身分識別從主控台應用程式呼叫 MICROSOFT GRAPH API](./quickstart-v2-netcore-daemon.md)。
- 的參考檔：
  - 具現化[ConfidentialClientApplication](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)
  - 呼叫[AcquireTokenForClient](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder)
- 其他範例/教學課程：
  - [microsoft-身分識別平臺-主控台-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon)功能是簡單的 .net Core daemon 主控台應用程式，可顯示查詢 Microsoft Graph 的租使用者使用者。

    ![拓撲](media/scenario-daemon-app/daemon-app-sample.svg)

    相同的範例也會說明憑證的變化。

    ![拓撲](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [webapp](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon)是 ASP.NET 的 MVC web 應用程式，它會使用應用程式的身分識別，而不是代表使用者，從 Microsoft Graph 同步處理資料。 此範例也會說明系統管理員同意流程。

    ![拓撲](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="pythontabpython"></a>[Python](#tab/python)

試用快速入門[取得權杖，並使用應用程式的身分識別從 Python 主控台應用程式呼叫 MICROSOFT GRAPH API](./quickstart-v2-python-daemon.md)。

# <a name="javatabjava"></a>[Java](#tab/java)

MSAL JAVA 目前為公開預覽狀態。 如需詳細資訊，請參閱[MSAL JAVA dev samples](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples)。

---

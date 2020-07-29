---
title: 將呼叫 web Api 的 daemon 應用程式移至生產環境-Microsoft 身分識別平臺 |Azure
description: 瞭解如何將會呼叫 web Api 的 daemon 應用程式移至生產環境
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
ms.openlocfilehash: e5d25169aa3daee33a9f02e605cbff91776dc7f1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80885424"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>呼叫 web Api 的 Daemon 應用程式-移至生產環境

現在您已瞭解如何取得並使用權杖來進行服務對服務呼叫，請瞭解如何將您的應用程式移至生產環境。

## <a name="deployment---multitenant-daemon-apps"></a>部署-多組織使用者共用 daemon 應用程式

如果您是 ISV，並建立可在數個租使用者中執行的 daemon 應用程式，您必須確定租使用者系統管理員：

- 布建應用程式的服務主體。
- 將同意授與應用程式。

您必須向客戶說明如何執行這些作業。 如需詳細資訊，請參閱[要求對整個租使用者的同意](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>後續步驟

以下是一些可協助您深入瞭解的連結：

# <a name="net"></a>[.NET](#tab/dotnet)

- 快速入門：[使用應用程式的身分識別取得權杖，並從主控台應用程式呼叫 MICROSOFT GRAPH API](./quickstart-v2-netcore-daemon.md)。
- 的參考檔：
  - 具現化[ConfidentialClientApplication](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)。
  - 呼叫[AcquireTokenForClient](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder)。
- 其他範例/教學課程：
  - [microsoft-身分識別平臺-主控台-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon)功能是簡單的 .net Core daemon 主控台應用程式，可顯示租使用者查詢 Microsoft Graph。

    ![範例 daemon 應用程式拓撲](media/scenario-daemon-app/daemon-app-sample.svg)

    相同的範例也會說明使用憑證的變化：

    ![範例 daemon 應用程式拓撲-憑證](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [webapp](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon)是 ASP.NET 的 MVC web 應用程式，它會使用應用程式的身分識別（而不是代表使用者），從 Microsoft Graph 同步處理資料。 這個範例也會說明系統管理員同意流程。

    ![拓撲](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="python"></a>[Python](#tab/python)

試用快速入門[取得權杖，並使用應用程式的身分識別從 Python 主控台應用程式呼叫 MICROSOFT GRAPH API](./quickstart-v2-python-daemon.md)。

# <a name="java"></a>[Java](#tab/java)

MSAL JAVA 目前為公開預覽狀態。 如需詳細資訊，請參閱[MSAL JAVA dev samples](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples)。

---

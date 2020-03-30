---
title: 將調用 Web API 的守護程式應用移動到生產 - 微軟標識平臺 |蔚藍
description: 瞭解如何將調用 Web API 的守護進程應用移動到生產
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
ms.openlocfilehash: db5f52c95daf4e93c140b4c93f39dad19971319d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262616"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>調用 Web API 的守護程式應用 - 移動到生產

現在，您已經瞭解如何獲取和使用權杖進行服務到服務調用，請瞭解如何將應用移動到生產。

## <a name="deployment---multitenant-daemon-apps"></a>部署 - 多租戶守護進程應用

如果您是 ISV 創建可在多個租戶中運行的守護進程應用程式，則需要確保租戶管理員：

- 為申請提供服務主體。
- 同意申請。

您需要向客戶解釋如何執行這些操作。 有關詳細資訊，請參閱[請求整個租戶的同意](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>後續步驟

下面是一些説明您瞭解更多資訊的連結：

# <a name="net"></a>[.NET](#tab/dotnet)

- 快速入門：[獲取權杖，並使用應用的標識從主控台應用調用 Microsoft 圖形 API。](./quickstart-v2-netcore-daemon.md)
- 參考文檔：
  - 具現化[機密用戶端應用程式](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)。
  - 調用[獲取權杖用戶端](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder)。
- 其他示例/教程：
  - [Microsoft 身份-平臺主控台-守護進程](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon)具有一個簡單的 .NET Core 守護進程主控台應用程式，該應用程式顯示查詢 Microsoft 圖形的租戶的使用者。

    ![示例守護進程應用拓撲](media/scenario-daemon-app/daemon-app-sample.svg)

    同一示例還說明了證書的變體：

    ![示例守護進程應用拓撲 - 證書](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [微軟-身份-平臺-aspnet-webapp-守護程式](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon)具有ASP.NET MVC Web 應用程式，該應用程式使用應用程式的身份而不是代表使用者同步來自 Microsoft Graph 的資料。 此示例還說明了管理員同意過程。

    ![拓撲](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="python"></a>[Python](#tab/python)

嘗試快速入門[獲取權杖，並使用應用的標識從 Python 主控台應用調用 Microsoft 圖形 API。](./quickstart-v2-python-daemon.md)

# <a name="java"></a>[JAVA](#tab/java)

MSAL JAVA 當前處於公共預覽版中。 有關詳細資訊，請參閱[MSAL JAVA 開發示例](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples)。

---

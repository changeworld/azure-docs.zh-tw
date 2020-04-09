---
title: 將呼叫 Web API 的守護程式應用移動到生產 - 微軟識別平臺 |蔚藍
description: 瞭解如何將呼叫 Web API 的守護程式應用程式到生產
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
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885424"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>呼叫 Web API 的守護程式應用 - 移動到生產

現在,您已經瞭解如何獲取和使用權杖進行服務到服務調用,請瞭解如何將應用移動到生產。

## <a name="deployment---multitenant-daemon-apps"></a>部署 - 多租程式守護程式應用程式

如果您是 ISV 建立可在多個租戶中執行的守護程序應用程式,則需要確保租戶管理員:

- 為申請提供服務主體。
- 同意申請。

您需要向客戶解釋如何執行這些操作。 有關詳細資訊,請參閱[請求整個租戶的同意](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>後續步驟

下面是一些説明您瞭解更多資訊的連結:

# <a name="net"></a>[.NET](#tab/dotnet)

- 快速入門:[取得權杖,並使用應用的標識從控制台應用調用 Microsoft 圖形 API。](./quickstart-v2-netcore-daemon.md)
- 參考文件:
  - 實體化[機密客戶端應用程式](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)。
  - 呼叫[取權杖客戶端](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder)。
- 其他範例/教學:
  - [Microsoft 身份-平臺主控台-守護程式](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon)具有一個簡單的 .NET Core 守護程式控制台應用程式,該應用程式顯示查詢 Microsoft 圖形的租戶的使用者。

    ![範例守護程式應用程式的拓撲](media/scenario-daemon-app/daemon-app-sample.svg)

    同一示例還說明了憑證的變體:

    ![範例守護程式應用程式的拓撲 - 憑證](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [微軟-身份-平臺-aspnet-webapp-守護程式](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon)具有ASP.NET MVC Web 應用程式,該應用程式使用應用程式的身份而不是代表使用者同步來自 Microsoft Graph 的數據。 此示例還說明了管理員同意過程。

    ![拓撲](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="python"></a>[Python](#tab/python)

嘗試快速入門[獲取權杖,並使用應用的標識從 Python 主控台應用呼叫 Microsoft 圖形 API。](./quickstart-v2-python-daemon.md)

# <a name="java"></a>[Java](#tab/java)

MSAL Java 當前處於公共預覽版中。 有關詳細資訊,請參閱[MSAL Java 開發範例](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples)。

---

---
title: UWP 考慮（MSAL.NET） |Azure
titleSuffix: Microsoft identity platform
description: 瞭解搭配適用于 .NET 的 Microsoft 驗證程式庫（MSAL.NET）使用通用 Windows 平臺（UWP）的考慮。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 0654bce86cf5fb0b5bd117e444721e95f137dd47
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82652684"
---
# <a name="considerations-for-using-universal-windows-platform-with-msalnet"></a>搭配 MSAL.NET 使用通用 Windows 平臺的考慮
使用通用 Windows 平臺（UWP）搭配 MSAL.NET 的應用程式開發人員應該考慮本文所提供的概念。

## <a name="the-usecorporatenetwork-property"></a>UseCorporateNetwork 屬性
在 Windows 執行階段（WinRT）平臺上， `PublicClientApplication` 具有布林值屬性 `UseCorporateNetwork` 。 此屬性可讓 Windows 8.1 應用程式和 UWP 應用程式受益于整合式 Windows 驗證（IWA）（如果使用者已登入具有同盟 Azure Active Directory （Azure AD）租使用者的帳戶）。 已登入作業系統的使用者也可以使用單一登入（SSO）。 當您設定 `UseCorporateNetwork` 屬性時，MSAL.NET 會使用 web 驗證訊息代理程式（WAB）。

> [!IMPORTANT]
> 將 `UseCorporateNetwork` 屬性設定為 true 時，會假設應用程式開發人員已在應用程式中啟用 IWA。 若要啟用 IWA：
> - 在 UWP 應用程式的 `Package.appxmanifest` [**功能**] 索引標籤上，啟用下列功能：
>   - **企業驗證**
>   - **私人網路 (用戶端和伺服器)**
>   - **共用使用者憑證**

預設不會啟用 IWA，因為 Microsoft Store 需要高層級的驗證，才能接受要求企業驗證或共用使用者憑證功能的應用程式。 並非所有開發人員都想要執行此層級的驗證。

在 UWP 平臺上，基礎的 WAB 執行在啟用條件式存取的企業案例中無法正常運作。 當使用者嘗試使用 Windows Hello 登入時，會看到此問題的徵兆。 當要求使用者選擇憑證時：

- 找不到 PIN 的憑證。
- 使用者選擇憑證之後，系統不會提示他們輸入 PIN。

您可以嘗試使用替代方法（例如使用者名稱-密碼和電話驗證）來避免這個問題，但體驗並不好。

## <a name="troubleshooting"></a>疑難排解

某些客戶在特定的企業環境中回報了下列登入錯誤，他們知道他們有網際網路連線，而且該連線與公用網路搭配運作。

```Text
We can't connect to the service you need right now. Check your network connection or try this again later.
```

您可以確定 WAB （基礎 Windows 元件）允許私人網路，以避免此問題。 您可以藉由設定登錄機碼來執行此動作：

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

如需詳細資訊，請參閱[Web 驗證訊息代理程式-Fiddler](https://docs.microsoft.com/windows/uwp/security/web-authentication-broker#fiddler)。

## <a name="next-steps"></a>後續步驟
下列範例會提供詳細資訊。

範例 | 平台 | 描述 
|------ | -------- | -----------|
|[active directory-dotnet-原生-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | 使用 MSAL.NET 的 UWP 用戶端應用程式。 它會存取使用 Azure AD 2.0 端點進行驗證之使用者的 Microsoft Graph。 <br>![拓撲](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[active directory-xamarin-native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS、Android、UWP | 簡單的 Xamarin Forms 應用程式，示範如何使用 MSAL 來驗證 Microsoft 個人帳戶，並透過 Azure AD 2.0 端點 Azure AD。 它也會說明如何存取 Microsoft Graph 並顯示產生的權杖。 <br>![拓撲](media/msal-net-uwp-considerations/topology-xamarin-native.png)|

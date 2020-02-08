---
title: UWP 考慮（MSAL.NET） |Azure
titleSuffix: Microsoft identity platform
description: 瞭解使用通用 Windows 平臺搭配適用于 .NET 的 Microsoft 驗證程式庫（MSAL.NET）時的特定考慮。
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
ms.openlocfilehash: 4803b2bda63ef0e14137aaafe95a422089e7f671
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2020
ms.locfileid: "77083672"
---
# <a name="universal-windows-platform-specific-considerations-with-msalnet"></a>MSAL.NET 的通用 Windows 平臺特定考慮
在 UWP 上，使用 MSAL.NET 時，您必須考慮幾個事項。

## <a name="the-usecorporatenetwork-property"></a>UseCorporateNetwork 屬性
在 WinRT 平臺中，`PublicClientApplication` 具有下列布林值屬性 ``UseCorporateNetwork``。 此屬性可讓 Win 8.1 和 UWP 應用程式受益于整合式 Windows 驗證（也就是使用與作業系統登入之使用者的 SSO），如果使用者是以同盟 Azure AD 租使用者中的帳戶登入。 當您設定這個屬性時，MSAL.NET 會利用 WAB （Web 驗證訊息代理程式）。

> [!IMPORTANT]
> 將此屬性設定為 true 時，會假設應用程式開發人員已在應用程式中啟用整合式 Windows 驗證（IWA）。 針對此：
> - 在 UWP 應用程式的 [``Package.appxmanifest``] 的 [**功能**] 索引標籤中，啟用下列功能：
>   - 企業驗證
>   - 私人網路 (用戶端和伺服器)
>   - 共用使用者憑證

根據預設，IWA 不會啟用，因為要求企業驗證或共用使用者憑證功能的應用程式需要接受較高層級的驗證，才能進入 Windows Store，而並非所有開發人員都想要執行更高的許可權。驗證層級。

在啟用條件式存取的企業案例中，UWP 平臺（WAB）上的基礎執行無法正常運作。 徵兆是使用者嘗試使用 Windows hello 登入，並建議選擇憑證，但：

- 找不到 pin 的憑證，
- 或者，使用者會選擇它，但不會提示您輸入 Pin。

因應措施是使用替代方法（使用者名稱/密碼 + 電話驗證），但體驗並不理想。

## <a name="troubleshooting"></a>疑難排解

有些客戶回報在某些特定的企業環境中，發生下列登入錯誤：

```Text
We can't connect to the service you need right now. Check your network connection or try this again later
```

他們知道他們有網際網路連線，而且可與公用網路搭配運作。

因應措施是確定 WAB （基礎 Windows 元件）允許私人網路。 您可以藉由設定登錄機碼來執行此動作：

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

如需詳細資訊，請參閱[Web 驗證訊息代理程式-Fiddler](https://docs.microsoft.com/windows/uwp/security/web-authentication-broker#fiddler)。

## <a name="next-steps"></a>後續步驟
下列範例中會提供更多詳細資料：

範例 | 平台 | 描述 
|------ | -------- | -----------|
|[active directory-dotnet-原生-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | 使用 msal.net 的通用 Windows 平臺用戶端應用程式，存取使用 Azure AD v2.0 端點進行驗證之使用者的 Microsoft Graph。 <br>![拓撲](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS、Android、UWP | 簡單的 Xamarin Forms 應用程式展示如何使用 MSAL 來驗證 MSA 和透過 AAD v2.0 端點的 Azure AD，並使用產生的權杖來存取 Microsoft Graph。 <br>![拓撲](media/msal-net-uwp-considerations/topology-xamarin-native.png)|

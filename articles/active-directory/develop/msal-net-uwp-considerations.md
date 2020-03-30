---
title: UWP 注意事項（MSAL.NET） |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解有關將通用 Windows 平臺 （UWP） 與 Microsoft 身份驗證庫一起使用 .NET （MSAL.NET） 的注意事項。
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2eeec28569cf31af4542d6cd7aca1fb27d77b1e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132519"
---
# <a name="considerations-for-using-universal-windows-platform-with-msalnet"></a>使用通用 Windows 平臺MSAL.NET的注意事項
使用通用 Windows 平臺 （UWP） MSAL.NET的應用程式開發人員應考慮本文介紹的概念。

## <a name="the-usecorporatenetwork-property"></a>使用公司網路屬性
在 Windows 運行時 （WinRT）`PublicClientApplication`平臺上，具有布林屬性`UseCorporateNetwork`。 如果使用者登錄到具有聯合 Azure 活動目錄 （Azure AD） 租戶的帳戶，則此屬性使 Windows 8.1 應用程式和 UWP 應用程式受益于集成 Windows 身份驗證 （IWA）。 登錄到作業系統的使用者也可以使用單一登入 （SSO）。 設置屬性時，MSAL.NET`UseCorporateNetwork`使用 Web 身份驗證代理 （WAB）。

> [!IMPORTANT]
> 將`UseCorporateNetwork`屬性設置為 true 假定應用程式開發人員已在應用程式中啟用 IWA。 要啟用 IWA：
> - 在 UWP 應用程式的`Package.appxmanifest`的 "**功能"** 選項卡上，啟用以下功能：
>   - **企業驗證**
>   - **私人網路 (用戶端和伺服器)**
>   - **共用使用者證書**

預設情況下未啟用 IWA，因為 Microsoft 應用商店在接受請求企業身份驗證或共用使用者證書功能的應用程式之前需要高級別的驗證。 並非所有開發人員都希望執行此級別的驗證。

在 UWP 平臺上，基礎 WAB 實現在啟用條件訪問的企業方案中無法正常工作。 當使用者嘗試使用 Windows Hello 登錄時，他們會看到此問題的症狀。 當要求使用者選擇證書時：

- 找不到 PIN 的證書。
- 使用者選擇證書後，不會提示他們輸入 PIN。

您可以使用使用者名密碼和電話身份驗證等替代方法來避免此問題，但體驗不是很好。

## <a name="troubleshooting"></a>疑難排解

一些客戶在知道具有互聯網連接且該連接與公共網路配合使用的特定企業環境中報告了以下登錄錯誤。

```Text
We can't connect to the service you need right now. Check your network connection or try this again later.
```

通過確保 WAB（基礎 Windows 元件）允許私人網路絡來避免此問題。 您可以通過設置登錄機碼來執行此操作：

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

有關詳細資訊，請參閱[Web 身份驗證代理 - Fiddler](https://docs.microsoft.com/windows/uwp/security/web-authentication-broker#fiddler)。

## <a name="next-steps"></a>後續步驟
以下示例提供了詳細資訊。

範例 | Platform | 描述 
|------ | -------- | -----------|
|[活動-目錄-點網-本機-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | 使用MSAL.NET的 UWP 用戶端應用程式。 它訪問使用 Azure AD 2.0 終結點進行身份驗證的使用者的 Microsoft 圖形。 <br>![拓撲](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[活動-目錄-xamarin-本機-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS、Android、UWP | 一個簡單的 Xamarin 表單應用，演示如何使用 MSAL 通過 Azure AD 2.0 終結點對 Microsoft 個人帳戶和 Azure AD 進行身份驗證。 它還演示如何訪問 Microsoft 圖形並顯示生成的權杖。 <br>![拓撲](media/msal-net-uwp-considerations/topology-xamarin-native.png)|

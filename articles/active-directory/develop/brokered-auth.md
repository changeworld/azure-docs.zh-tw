---
title: 在 Android 中進行代理身份驗證 |蔚藍
titlesuffix: Microsoft identity platform
description: Microsoft 身份平臺中 Android 的代理身份驗證&授權的概述
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/14/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman, hahamil, brianmel
ms.openlocfilehash: a734589178438fd65d9a2d156fd91fc82807f578
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76697892"
---
# <a name="brokered-authentication-in-android"></a>Android 中的代理身份驗證

您必須使用 Microsoft 的身份驗證代理之一參與設備範圍的單一登入 （SSO） 並滿足組織條件訪問策略。 與經紀商集成提供以下好處：

- 設備單點登錄
- 條件訪問：
  - Intune 應用程式保護
  - 設備註冊（工作場所加入）
  - 行動裝置管理
- 設備範圍的帳戶管理
  -  通過 Android 帳戶管理器&帳戶設置
  - "工作帳戶" - 自訂帳戶類型

在 Android 上，微軟身份驗證代理是[微軟身份驗證器應用](https://play.google.com/store/apps/details?id=com.azure.authenticator)和[Intune 公司門戶](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)中包含的元件

> [!TIP]
> 只有一個承載代理的應用程式一次將作為代理處於活動狀態。 哪個應用程式作為代理處於活動狀態取決於設備上的安裝順序。 第一個要安裝的，或設備上的最後一個存在，將成為活動代理。

下圖說明瞭你的應用、微軟身份驗證庫 （MSAL） 和 Microsoft 身份驗證代理之間的關係。

![代理部署圖](./media/brokered-auth/brokered-deployment-diagram.png)

## <a name="installing-apps-that-host-a-broker"></a>安裝承載代理的應用

代理託管應用可以在任何時候由設備擁有者從其應用商店（通常是 Google Play 商店）安裝。 但是，某些 API（資源）受條件訪問策略的保護，這些策略要求設備具有：

- 註冊（加入工作場所）和/或
- 註冊在裝置管理或
- 註冊 Intune 應用保護

如果設備尚未安裝代理應用，MSAL 會指示使用者在應用嘗試以對話模式獲取權杖時立即安裝代理應用。 然後，應用將需要引導使用者完成使設備符合所需策略的步驟。

## <a name="effects-of-installing-and-uninstalling-a-broker"></a>安裝和卸載代理的效果

### <a name="when-a-broker-is-installed"></a>安裝代理時

當代理安裝在設備上時，所有後續的互動式權杖請求（對`acquireToken()`的調用）都由代理處理，而不是由 MSAL 在本地處理。 以前對 MSAL 可用的任何 SSO 狀態對代理不可用。 因此，使用者需要再次進行身份驗證，或從設備已知的現有帳戶清單中選擇帳戶。

安裝代理不需要使用者重新登錄。 僅當使用者需要解析 時`MsalUiRequiredException`，下一個請求才會轉到代理。 `MsalUiRequiredException`引發的原因有很多，需要以對話模式解決。 以下是一些常見原因：

- 使用者更改了與其帳戶關聯的密碼。
- 使用者帳戶不再滿足條件訪問策略。
- 使用者撤銷了對應用與其帳戶關聯的同意。

### <a name="when-a-broker-is-uninstalled"></a>卸載代理時

如果只安裝了一個代理託管應用，並且它被刪除，則使用者將需要再次登錄。 卸載活動代理會從設備中刪除帳戶和關聯的權杖。

如果 Intune 公司門戶已安裝，並且作為活動代理運行，並且也安裝了 Microsoft 身份驗證器，則如果卸載 Intune 公司門戶（活動代理），使用者將需要再次登錄。 一旦他們再次登錄，微軟身份驗證器應用程式將成為活動代理。

## <a name="integrating-with-a-broker"></a>與經紀人集成

### <a name="generating-a-redirect-uri-for-a-broker"></a>為代理生成重定向 URI

您必須註冊與代理相容的重定向 URI。 代理的重定向 URI 需要包括應用的包名稱，以及應用簽名的 base64 編碼表示形式。

重定向 URI 的格式為：`msauth://<yourpackagename>/<base64urlencodedsignature>`

使用應用的簽名金鑰生成 Base64 URL 編碼簽名。 下面是一些使用調試簽名金鑰的示例命令：

#### <a name="macos"></a>macOS

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

#### <a name="windows"></a>Windows

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

有關對應用進行簽名的資訊，請參閱[對應用進行簽名](https://developer.android.com/studio/publish/app-signing)。

> [!IMPORTANT]
> 將生產簽名金鑰用於應用的生產版本。

### <a name="configure-msal-to-use-a-broker"></a>將 MSAL 配置為使用代理

要在應用中使用代理，您必須證明已配置了代理重定向。 例如，通過在 MSAL 設定檔中包括以下內容，包括代理啟用的重定向 URI，並指示您註冊了它：

```javascript
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

> [!TIP]
> 新的 Azure 門戶應用註冊 UI 可説明您生成代理重定向 URI。 如果您使用較舊的體驗註冊應用，或者使用 Microsoft 應用註冊門戶註冊了應用，則可能需要生成重定向 URI 並手動更新門戶中的重定向 URI 清單。

### <a name="broker-related-exceptions"></a>與經紀人相關的例外

MSAL 以兩種方式與經紀商通信：

- 代理綁定服務
- 安卓帳戶管理器

MSAL 首先使用代理綁定服務，因為調用此服務不需要任何 Android 許可權。 如果綁定到綁定服務失敗，MSAL 將使用 Android 帳戶管理器 API。 僅當應用已被授予許可權時，MSAL`"READ_CONTACTS"`才會這樣做。

如果得到錯誤代碼`MsalClientException``"BROKER_BIND_FAILURE"`，則有兩個選項：

- 要求使用者禁用 Microsoft 身份驗證器應用和 Intune 公司門戶的電源優化。
- 要求使用者授予`"READ_CONTACTS"`許可權

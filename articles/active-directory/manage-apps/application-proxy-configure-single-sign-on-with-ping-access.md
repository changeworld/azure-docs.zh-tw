---
title: 標頭型驗證搭配 Azure AD 應用程式 Proxy 的 PingAccess | Microsoft Docs
description: 使用 PingAccess 與應用程式 Proxy 來發行應用程式可支援標頭型驗證。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3fb94629262519f8cfa5da72ee343726aa7d1c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77367975"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>使用應用程式 Proxy 與 PingAccess 的單一登入之標頭式驗證

Azure 活動目錄 （Azure AD） 應用程式代理已與 PingAccess 合作，以便 Azure AD 客戶可以訪問更多應用程式。 PingAccess 會展開[現有應用程式 Proxy 供應項目](application-proxy.md)以包含單一登入存取使用標頭進行驗證的應用程式。

## <a name="whats-pingaccess-for-azure-ad"></a>什麼是 Azure AD 的 Ping Access？

使用 Azure AD 的 PingAccess，您可以為使用者提供對使用標頭進行身份驗證的應用程式的存取權限和單一登入 （SSO）。 應用程式 Proxy 會如同任何其他應用程式一樣處理這些應用程式，使用 Azure AD 驗證存取，然後透過連接器服務傳遞流量。 PingAccess 位於應用程式前面，並將 Azure AD 的訪問權杖轉換為標頭。 然後，應用程式以它可以讀取的格式接收身份驗證。

使用者在登入使用您公司的應用程式時，將不會注意到什麼不同。 這些還是可以在任何裝置上從任何地方運作。 應用程式代理連接器將遠端流量定向到所有應用，而不考慮其身份驗證類型，因此它們仍會自動平衡負載。

## <a name="how-do-i-get-access"></a>如何取得存取權？

由於此方案來自 Azure 活動目錄和 PingAccess 之間的合作關係，因此需要兩個服務的許可證。 不過，Azure Active Directory Premium 訂用帳戶所包含的基本 PingAccess 授權最多可涵蓋 20 個應用程式。 如果您需要發佈 20 個以上的標頭應用程式，可以從 PingAccess 購買額外的授權。

有關詳細資訊，請參閱[Azure 活動目錄版本](../fundamentals/active-directory-whatis.md)。

## <a name="publish-your-application-in-azure"></a>在 Azure 中發佈應用程式

本文供人們首次使用此方案發佈應用程式。 除了詳細說明發佈步驟外，它還指導您開始使用應用程式代理和 PingAccess。 如果已配置了兩個服務，但希望對發佈步驟進行刷新，請跳到["使用應用程式代理將應用程式添加到 Azure AD"](#add-your-application-to-azure-ad-with-application-proxy)部分。

> [!NOTE]
> 因為此案例是 Azure AD 和 PingAccess 之間的合作關係，有些指示存在於 Ping 身分識別站台。

### <a name="install-an-application-proxy-connector"></a>安裝應用程式 Proxy 連接器

如果已啟用應用程式代理並安裝連接器，則可以跳過此部分，然後轉到[使用應用程式代理將應用程式添加到 Azure AD。](#add-your-application-to-azure-ad-with-application-proxy)

應用程式代理連接器是 Windows Server 服務，可將流量從遠端員工定向到已發佈的應用程式。 有關更詳細的安裝說明，請參閱[教程：在 Azure 活動目錄中添加用於遠端存取的本地應用程式](application-proxy-add-on-premises-application.md)。

1. 以應用程式管理員身份登錄到[Azure 活動目錄門戶](https://aad.portal.azure.com/)。 將顯示**Azure 活動目錄管理中心**頁。
1. 選擇**Azure 活動目錄** > **應用程式代理** > **下載連接器服務**。 將顯示**應用程式代理連接器下載**頁面。

   ![應用程式代理連接器下載](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)

1. 遵循安裝指示進行。

下載連接器應自動為目錄啟用應用程式代理，但如果沒有，則可以選擇**啟用應用程式代理**。

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>使用應用程式代理將應用程式添加到 Azure AD

您需要在 Azure 入口網站中採取兩個動作。 首先，您必須使用應用程式 Proxy 發佈應用程式。 然後，您需要收集有關應用程式的某些資訊，您可以在 PingAccess 步驟中使用這些資訊。

#### <a name="publish-your-application"></a>發佈您的應用程式

您首先必須發佈應用程式。 此操作涉及：

- 將本地應用程式添加到 Azure AD
- 分配使用者以測試應用程式並選擇基於標頭的 SSO
- 設置應用程式的重定向 URL
- 授予使用者和其他應用程式使用本地應用程式的許可權

要發佈您自己的本地應用程式，請執行：

1. 如果沒有在最後一節中，請以應用程式管理員身份登錄到 Azure[活動目錄門戶](https://aad.portal.azure.com/)。
1. 選擇**企業應用程式** > **新應用程式** > **添加本地應用程式**。 將顯示 **"添加您自己的本地應用程式**"頁。

   ![添加您自己的本地應用程式](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
1. 填寫所需欄位，包含有關新應用程式的資訊。 使用下面的指南進行設置。

   > [!NOTE]
   > 有關此步驟的更詳細演練，請參閱[將本地應用添加到 Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)。

   1. **內部 URL**：通常，當您在公司網路上時，您會提供將您帶到應用登錄頁面的 URL。 對於此方案，連接器需要將 PingAccess 代理視為應用程式的首頁。 使用此格式︰`https://<host name of your PingAccess server>:<port>`。 連接埠預設為 3000，但您可以在 PingAccess 中設定它。

      > [!WARNING]
      > 對於這種類型的單登錄，內部 URL 必須使用`https`並且不能使用`http`。

   1. **預身份驗證方法**：選擇**Azure 活動目錄**。
   1. **在標題中翻譯 URL：** 選擇**否**。

   > [!NOTE]
   > 如果這是您的第一個應用程式，請在變更 PingAccess 設定時，使用連接埠 3000 啟動並返回以更新這項設定。 對於後續應用程式，埠將需要與在 PingAccess 中配置的攔截器匹配。 深入了解 [PingAccess 中的接聽程式](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=reference/ui/pa_c_Listeners.html)。

1. 選取 [加入]****。 將顯示新應用程式的概述頁。

現在分配使用者進行應用程式測試，並選擇基於標頭的單一登入：

1. 從應用程式側邊欄中，選擇 **"使用者"和"組** > **添加使用者** > **和組"（\<數位>已選擇）。** 將顯示一個使用者和組清單供您選擇。

   ![顯示使用者和組的清單](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)

1. 選擇使用者進行應用程式測試，然後**選擇**。 請確定此測試帳戶可存取內部部署應用程式。
1. 選擇 **"分配**"。
1. 從應用程式側邊欄中，選擇**基於****單一登入標頭** > 。

   > [!TIP]
   > 如果這是您第一次使用標頭式單一登入，您需要安裝 PingAccess。 若要確定您的 Azure 訂用帳戶會與 PingAccess 安裝自動產生關聯，請使用此單一登入頁面上的連結來下載 PingAccess。 您現在可以開啟下載網站，或稍後返回此頁面。

   ![顯示基於頭的登錄螢幕和 PingAccess](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)

1. 選取 [儲存]****。

然後，請確保您的重定向 URL 已設置為外部 URL：

1. 從**Azure 活動目錄管理中心**側邊欄中，選擇**Azure 活動目錄** > **應用註冊**。 將顯示應用程式清單。
1. 選取您的應用程式。
1. 選擇**重定向 URI**旁邊的連結，顯示為 Web 和公共用戶端設置的重定向 URI 數。 ** \<將顯示應用程式名稱> - 身份驗證**頁。
1. 檢查之前分配給應用程式的外部 URL 是否位於**重定向 URI**清單中。 如果不是，請立即添加外部 URL，使用 Web**的重定向**URI 類型，然後選擇 **"保存**"。

最後，設置本地應用程式，以便使用者具有讀取存取許可權，其他應用程式具有讀/寫存取權限：

1. 從應用程式**註冊**側邊欄中，選擇 API**許可權** > **添加** > **微軟 API** > **微軟圖形**的許可權 。 將顯示**Microsoft 圖形**的請求**API 許可權**頁，其中包含 Windows Azure 活動目錄的 API。

   ![顯示請求 API 許可權頁](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

1. 選擇**委派許可權** > **使用者** > **。**
1. 選擇**應用程式許可權** > **應用程式** > **.讀取寫入。所有**。
1. 選取 [新增權限]****。
1. 在**API 許可權**頁中，選擇 **"授予\<管理員同意>目錄名稱**。

#### <a name="collect-information-for-the-pingaccess-steps"></a>收集 PingAccess 步驟的資訊

您需要收集這三條資訊（所有 GUID）才能使用 PingAccess 設置應用程式：

| Azure AD 欄位的名稱 | PingAccess 欄位的名稱 | 資料格式 |
| --- | --- | --- |
| **應用程式 (用戶端) 識別碼** | **用戶端 ID** | GUID |
| **目錄 (租用戶) 識別碼** | **發行** | GUID |
| `PingAccess key` | **用戶端機密** | 隨機字串 |

要收集此資訊，

1. 從**Azure 活動目錄管理中心**側邊欄中，選擇**Azure 活動目錄** > **應用註冊**。 將顯示應用程式清單。
1. 選取您的應用程式。 將顯示應用程式**的應用註冊**頁。

   ![應用程式的註冊概述](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)

1. 在**應用程式（用戶端）ID**值旁邊，選擇 **"複製到剪貼簿**"圖示，然後複製並保存它。 稍後將此值指定為 PingAccess 的用戶端 ID。
1. 接下來目錄 **（租戶）ID**值，也選擇 **"複製到剪貼簿**"，然後複製並保存它。 稍後將此值指定為 PingAccess 的頒發者。
1. 從應用程式註冊為應用程式**註冊**側邊欄，選擇**證書和機密** > **新用戶端機密**。 將顯示 **"添加用戶端機密**"頁。

   ![顯示"添加用戶端機密頁"](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)

1. 在**描述**`PingAccess key`中，鍵入 。
1. 在 **"過期"** 下，選擇如何設置 PingAccess 鍵：**在 1 年內**，在 2**年**，或**永遠不會**。
1. 選取 [加入]****。 PingAccess 鍵顯示在用戶端機密表中，其中隨機字串在**VALUE**欄位中自動填滿。
1. 在 PingAccess 鍵的**VALUE**欄位旁邊，選擇 **"複製到剪貼簿**"圖示，然後複製並保存它。 稍後將此值指定為 PingAccess 的用戶端機密。

**更新欄位`acceptMappedClaims`：**

1. 以應用程式管理員身份登錄到[Azure 活動目錄門戶](https://aad.portal.azure.com/)。
1. 選擇**Azure 活動目錄** > **應用註冊**。 將顯示應用程式清單。
1. 選取您的應用程式。
1. 從應用程式**註冊**頁面的邊欄中，選擇 **"清單**"。 將顯示應用程式註冊的清單 JSON 代碼。
1. 搜索欄位`acceptMappedClaims`，並將值更改為`True`。
1. 選取 [儲存]****。

### <a name="use-of-optional-claims-optional"></a>使用可選聲明（可選）

可選聲明允許您添加每個使用者和租戶具有的標準但未包含的聲明。 您可以通過修改應用程式清單為應用程式佈建可選聲明。 有關詳細資訊，請參閱[瞭解 Azure AD 應用程式清單一文](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest/)

將電子郵件地址包含在 PingAccess 將使用access_token中的示例：
```
    "optionalClaims": {
        "idToken": [],
        "accessToken": [
            {
                "name": "email",
                "source": null,
                "essential": false,
                "additionalProperties": []
            }
        ],
        "saml2Token": []
    },
```

### <a name="use-of-claims-mapping-policy-optional"></a>使用宣告對應策略（可選）

對 AzureAD 中不存在的屬性[的宣告對應策略（預覽](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping#claims-mapping-policy-properties)）。 宣告對應允許您通過添加由 ADFS 或使用者物件支援的其他自訂聲明將舊的 prem 應用遷移到雲

要使應用程式使用自訂聲明並包含其他欄位，請確保還[創建了自訂宣告對應策略並將其分配給應用程式](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment)。

> [!NOTE]
> 若要使用自訂宣告，您必須已定義自訂原則且已指派給應用程式。 此原則應包含所有必要的自訂屬性。
>
> 您可以通過 PowerShell 或 Microsoft 圖形執行策略定義和分配。 如果您在 PowerShell 中執行，則可能需要首先使用`New-AzureADPolicy`，然後將其分配給使用`Add-AzureADServicePrincipalPolicy`的應用程式。 有關詳細資訊，請參閱[宣告對應策略分配](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment)。

範例：
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id
```

### <a name="enable-pingaccess-to-use-custom-claims"></a>啟用 PingAccess 使用自訂聲明

啟用 PingAccess 使用自訂聲明是可選的，但如果希望應用程式使用其他聲明，則需要使用。

在以下步驟中配置 PingAccess 時，您將創建的 Web 會話（設置->訪問>Web 會話）必須取消選擇 **"請求設定檔**"，**並刷新使用者屬性**設置為 **"否"**

## <a name="download-pingaccess-and-configure-your-application"></a>下載 PingAccess 並配置應用程式

現在您已完成所有的 Azure Active Directory 安裝步驟，可以移至設定 PingAccess。

此方案的 PingAccess 部分的詳細步驟將繼續在 Ping 標識文檔中。 按照為 Azure [AD 配置 PingAccess 中的說明進行操作，以保護在](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html)Ping 標識網站上使用 Microsoft Azure AD 應用程式代理髮布的應用程式。

這些步驟可説明您安裝 PingAccess 並設置 PingAccess 帳戶（如果您還沒有帳戶）。 然後，要創建 Azure AD OpenID 連接 （OIDC） 連接，請設置一個權杖提供程式，該提供程式具有從 Azure AD 門戶複製的**目錄（租戶）ID**值。 接下來，要在 PingAccess 上創建 Web 會話，請使用**應用程式（用戶端）ID**和`PingAccess key`值。 之後，您可以設定身分識別對應，並建立虛擬主機、網站和應用程式。

### <a name="test-your-application"></a>測試您的應用程式

完成所有這些步驟後，應用程式應啟動並運行。 要測試它，請打開瀏覽器並導航到在 Azure 中發佈應用程式時創建的外部 URL。 使用分配給應用程式的測試帳戶登錄。

## <a name="next-steps"></a>後續步驟

- [為 Azure AD 配置 PingAccess 以保護使用 Microsoft Azure AD 應用程式代理髮布的應用程式](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html)
- [Azure Active Directory 中的應用程式單一登入](what-is-single-sign-on.md)
- [針對應用程式 Proxy 問題和錯誤訊息進行疑難排解](application-proxy-troubleshoot.md)

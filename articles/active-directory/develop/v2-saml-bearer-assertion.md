---
title: 微軟身份平臺& SAML 承載斷言流 |蔚藍
description: 瞭解如何從 Microsoft 圖形獲取資料，而無需使用 SAML 承載斷言流提示使用者獲取憑據。
services: active-directory
documentationcenter: ''
author: umeshbarapatre
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 16f30473ded5f1de5dc94c1cff9da96165b1a01c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76700204"
---
# <a name="microsoft-identity-platform-and-oauth-20-saml-bearer-assertion-flow"></a>微軟身份平臺和 OAuth 2.0 SAML 承載斷言流
OAuth 2.0 SAML 無記名斷言流允許您在用戶端需要使用現有信任關係時使用 SAML 斷言請求 OAuth 訪問權杖。 應用於 SAML 斷言的簽名提供授權應用的身份驗證。 SAML 斷言是由標識提供程式發出並由服務提供者使用的 XML 安全權杖。 服務提供者依賴其內容來標識斷言的主題，以便用於安全相關目的。

SAML 斷言發佈到 OAuth 權杖終結點。  終結點處理斷言，並根據應用的事先批准頒發訪問權杖。 用戶端不需要具有或存儲刷新權杖，也不需要將用戶端金鑰傳遞給權杖終結點。

SAML 承載斷言流在未提示使用者獲取憑據的情況下從 Microsoft 圖形 API（僅支援委派的許可權）提取資料時非常有用。 在這種情況下，用戶端憑據授予（後臺進程首選）不起作用。

對於執行基於互動式瀏覽器的登錄以獲取 SAML 斷言，然後想要添加對 OAuth 受保護 API（如 Microsoft 圖形）的訪問的應用程式，可以發出 OAuth 請求以獲取 API 的訪問權杖。 當瀏覽器重定向到 Azure AD 以對使用者進行身份驗證時，瀏覽器將從 SAML 登錄中選取會話，使用者無需輸入其憑據。

對於使用標識提供程式（如與 Azure 活動目錄聯合聯合的活動目錄聯合服務 （ADFS））進行身份驗證的使用者，還支援 OAuth SAML 承載斷言流。  從 ADFS 獲得的 SAML 斷言可用於 OAuth 流中，以驗證使用者。

![歐思流](./media/v2-saml-bearer-assertion/1.png)

## <a name="call-graph-using-saml-bearer-assertion"></a>使用 SAML 承載斷言的呼叫圖
現在，讓我們瞭解如何以程式設計方式實際獲取 SAML 斷言。 此方法通過 ADFS 進行測試。 但是，這與支援以程式設計方式返回 SAML 斷言的任何標識提供程式一起工作。 基本過程是：獲取 SAML 斷言、獲取訪問權杖和訪問 Microsoft 圖形。

### <a name="prerequisites"></a>Prerequisites

在授權伺服器/環境 （Microsoft 365） 和 SAML 2.0 承載斷言 （ADFS） 的標識提供程式或頒發者之間建立信任關係。 要為單一登入配置 ADFS，並且作為標識提供程式，請參閱[本文](https://blogs.technet.microsoft.com/canitpro/2015/09/11/step-by-step-setting-up-ad-fs-and-enabling-single-sign-on-to-office-365/)。

在[門戶](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)中註冊應用程式：
1. 登錄到[門戶的應用註冊邊欄選項卡](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)（請注意，我們使用 v2.0 終結點進行圖形 API，因此需要在此門戶中註冊應用程式。 否則，我們本可以在 Azure 活動目錄中使用註冊。 
1. 選取 [新增註冊]****。
1. 當 **"註冊應用程式**"頁出現時，輸入應用程式的註冊資訊： 
    1. **名稱** - 輸入會顯示給應用程式使用者的有意義應用程式名稱。
    1. **支援的帳戶類型** - 選取要讓應用程式支援的帳戶。
    1. **重定向 URI（可選）** - 選擇要構建的應用類型、Web 或公共用戶端（移動&桌面），然後輸入應用程式的重定向 URI（或回復 URL）。
    1. 完成時，選取 [註冊]****。
1. 記下應用程式（用戶端）ID。
1. 在左側窗格中，選擇 **"證書&機密**"。 按一下"**用戶端機密**"部分中的 **"新用戶端機密**"。 複製新的用戶端金鑰，當您離開邊欄選項卡時，您將無法檢索。
1. 在左側窗格中，選擇**API 許可權**，然後**添加許可權**。 選擇**Microsoft 圖形**，然後**委派許可權**，然後選擇**Tasks.read，** 因為我們打算使用 Outlook 圖形 API。 

安裝[Postman](https://www.getpostman.com/)，這是測試示例請求所需的工具。  稍後，您可以將請求轉換為代碼。

### <a name="get-the-saml-assertion-from-adfs"></a>從 ADFS 獲取 SAML 斷言
使用 SOAP 信封創建 ADFS 終結點的 POST 請求以獲取 SAML 斷言：

![獲取 SAML 斷言](./media/v2-saml-bearer-assertion/2.png)

標題值：

![標題值](./media/v2-saml-bearer-assertion/3.png)

ADFS 請求正文：

![ADFS 請求正文](./media/v2-saml-bearer-assertion/4.png)

成功發佈此請求後，您應該從 ADFS 收到 SAML 斷言。 只需要**SAML：斷言**標記資料，將其轉換為 base64 編碼以用於其他請求。

### <a name="get-the-oauth2-token-using-the-saml-assertion"></a>使用 SAML 斷言獲取 OAuth2 權杖 
在此步驟中，使用 ADFS 斷言回應獲取 OAuth2 權杖。

1. 使用標頭值創建 POST 請求，如下所示：

    ![POST 要求](./media/v2-saml-bearer-assertion/5.png)
1. 在請求正文中，替換**client_id** **，client_secret**和**斷言**（base64 編碼 SAML 斷言獲得了上一步）：

    ![Request body](./media/v2-saml-bearer-assertion/6.png)
1. 成功請求後，您將收到來自 Azure 活動目錄的訪問權杖。

### <a name="get-the-data-with-the-oauth-token"></a>使用 Oauth 權杖獲取資料

收到訪問權杖後，調用圖形 API（本示例中的 Outlook 任務）。 

1. 創建具有上一步驟中獲取的訪問權杖的 GET 請求：

    ![GET 要求](./media/v2-saml-bearer-assertion/7.png)

1. 請求成功後，您將收到 JSON 回應。

## <a name="next-steps"></a>後續步驟

瞭解不同的[身份驗證流和應用程式方案](authentication-flows-app-scenarios.md)。
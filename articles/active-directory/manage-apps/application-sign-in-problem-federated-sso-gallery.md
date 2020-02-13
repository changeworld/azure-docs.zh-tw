---
title: 登入同盟單一登入資源庫應用程式時發生問題 |Microsoft Docs
description: 當登入您已使用 Azure AD 針對 SAML 型同盟單一登入設定之應用程式時所發生特定錯誤的指引
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: mimart
ms.reviewer: luleon, asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97954123b6fc31dce09282c08c702438cd64c476
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77159245"
---
# <a name="problems-signing-in-to-a-gallery-application-configured-for-federated-single-sign-on"></a>登入針對同盟單一登入設定之資源庫應用程式的問題

若要疑難排解以下的登入問題，建議您遵循這些建議，以取得更佳的診斷並自動解決步驟：

- 安裝[我的應用程式安全瀏覽器擴充](access-panel-extension-problem-installing.md)功能，以協助 Azure Active Directory （Azure AD）在使用 Azure 入口網站中的測試體驗時，提供更佳的診斷和解決方式。
- 使用 Azure 入口網站的 [應用程式設定] 頁面中的測試體驗，重現錯誤。 深入瞭解如何以[SAML 為基礎的單一登入應用程式](../azuread-dev/howto-v1-debug-saml-sso-issues.md)


## <a name="application-not-found-in-directory"></a>在目錄中找不到應用程式

*錯誤 AADSTS70001：在目錄中找不到識別碼為 ' HTTPs：\//contoso.com ' 的應用程式*。

**可能的原因**

從應用程式傳送至 SAML 要求中 Azure AD 的 `Issuer` 屬性，與 Azure AD 中針對應用程式所設定的識別碼值不相符。

**解決方案**

請確定 SAML 要求中的 `Issuer` 屬性符合 Azure AD 中設定的識別碼值。 如果您在 Azure 入口網站中使用具有我的應用程式安全瀏覽器擴充功能的[測試經驗](../azuread-dev/howto-v1-debug-saml-sso-issues.md)，則不需要手動執行這些步驟。

1.  開啟 [Azure 入口網站](https://portal.azure.com/)，然後以**全域管理員**或**共同管理員**身分登入。

1.  選取左邊主流覽功能表底部的 [**所有服務**]，以開啟 [ **Azure Active Directory] 延伸**模組。

1.  在篩選搜尋方塊中輸入 **"Azure Active Directory"** ，然後選取 [ **Azure Active Directory** ] 專案。

1.  從 Azure Active Directory 左側導覽功能表中選取 [**企業應用程式**]。

1.  選取 [所有應用程式]，以檢視所有應用程式的清單。

    若在這裡沒看到您要顯示的應用程式，請使用 [所有應用程式清單] 頂端的 [篩選] 控制項，並將 [顯示] 選項設定為 [所有應用程式]。

1.  選取您要設為單一登入的應用程式。

1.  在應用程式載入後，開啟 [基本 SAML 組態]。 確認 [識別碼] 文字方塊中的值符合錯誤中所顯示之識別碼值的值。



## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>回復位址與為應用程式設定的回復位址不符

*錯誤 AADSTS50011：回復位址 ' HTTPs：\//contoso.com ' 不符合為應用程式設定的回復位址*

**可能的原因**

SAML 要求中的 `AssertionConsumerServiceURL` 值不符合 Azure AD 中設定的回復 URL 值或模式。 SAML 要求中的 `AssertionConsumerServiceURL` 值是您在錯誤中看到的 URL。

**解決方案**

請確定 SAML 要求中的 `AssertionConsumerServiceURL` 值符合 Azure AD 中設定的 [回復 URL] 值。 如果您在 Azure 入口網站中使用具有我的應用程式安全瀏覽器擴充功能的[測試經驗](../azuread-dev/howto-v1-debug-saml-sso-issues.md)，則不需要手動執行這些步驟。

1.  開啟 [Azure 入口網站](https://portal.azure.com/)，然後以**全域管理員**或**共同管理員**身分登入。

1.  選取左邊主流覽功能表底部的 [**所有服務**]，以開啟 [ **Azure Active Directory] 延伸**模組。

1.  在篩選搜尋方塊中輸入 **"Azure Active Directory"** ，然後選取 [ **Azure Active Directory** ] 專案。

1.  從 Azure Active Directory 左側導覽功能表中選取 [**企業應用程式**]。

1.  選取 [所有應用程式]，以檢視所有應用程式的清單。

    若在這裡沒看到您要顯示的應用程式，請使用 [所有應用程式清單] 頂端的 [篩選] 控制項，並將 [顯示] 選項設定為 [所有應用程式]。

1.  選取您要設為單一登入的應用程式。

1.  在應用程式載入後，開啟 [基本 SAML 組態]。 確認或更新 [回復 URL] 文字方塊中的值，以符合 SAML 要求中的 `AssertionConsumerServiceURL` 值。    
    
當您更新 Azure AD 中的 [回復 URL] 值，而且它符合 SAML 要求中應用程式所傳送的值之後，您應該能夠登入應用程式。

## <a name="user-not-assigned-a-role"></a>使用者未指派角色

*錯誤 AADSTS50105：未將已登入的使用者 ' brian\@contoso.com ' 指派給應用程式的角色*。

**可能的原因**

尚未將 Azure AD 中應用程式的存取權授與使用者。

**解決方案**

若要直接將一或多個使用者指派給應用程式，請遵循下列步驟。 如果您在 Azure 入口網站中使用具有我的應用程式安全瀏覽器擴充功能的[測試經驗](../azuread-dev/howto-v1-debug-saml-sso-issues.md)，則不需要手動執行這些步驟。

1.  開啟 [Azure 入口網站](https://portal.azure.com/)，以**全域管理員**身分登入。

1.  選取左邊主流覽功能表底部的 [**所有服務**]，以開啟 [ **Azure Active Directory] 延伸**模組。

1.  在篩選搜尋方塊中輸入 **"Azure Active Directory**"，然後選取 [ **Azure Active Directory** ] 專案。

1.  從 Azure Active Directory 左側導覽功能表中選取 [**企業應用程式**]。

1.  選取 [所有應用程式]，以檢視所有應用程式的清單。

    若在這裡沒看到您要顯示的應用程式，請使用 [所有應用程式清單] 頂端的 [篩選] 控制項，並將 [顯示] 選項設定為 [所有應用程式]。

1.  從應用程式清單中，選取您想要指派給使用者的帳戶。

1.  應用程式載入後，從應用程式的左側導覽功能表中選取 [**使用者和群組**]。

1.  按一下 [使用者和群組] 清單頂端的 [新增] 按鈕，以開啟 [新增指派] 窗格。

1.  選取 [新增指派] 窗格中的 [使用者和群組] 選取器。

1. 在 [依名稱或電子郵件地址搜尋] 搜尋方塊中，針對您要新增的使用者輸入其完整名稱或電子郵件地址。

1. 將滑鼠停留在清單中的**使用者**上方，以顯示**核取方塊**。 按一下使用者設定檔相片或標誌旁邊的核取方塊，將使用者新增至 [已**選取**] 清單。

1. **選擇性：** 如果您想要**新增多個使用者**，請在 [**依名稱或電子郵件地址搜尋**] 搜尋方塊中輸入另一個完整名稱或電子郵件地址，然後按一下核取方塊，將使用者新增至 [已**選取**] 清單。

1. 當您完成選取使用者時，請按一下 [**選取**] 按鈕，將他們新增到要指派給應用程式的使用者和群組清單。

1. **選擇性：** 按一下 [**新增指派**] 窗格中的 [**選取角色**] 選取器，以選取要指派給您已選取之使用者的角色。

1. 按一下 [指派] 按鈕，將應用程式指派給選取的使用者。

一小段時間之後，您選取的使用者就能夠使用解決方案描述一節中所述的方法來啟動這些應用程式。

## <a name="not-a-valid-saml-request"></a>不是有效的 SAML 要求

*錯誤 AADSTS75005：要求不是有效的 Saml2 通訊協定訊息。*

**可能的原因**

Azure AD 不支援應用程式為單一登入傳送的 SAML 要求。 以下為一些常見問題：

-   SAML 要求中遺漏必要的欄位
-   SAML 要求編碼方法

**解決方案**

1. 捕捉 SAML 要求。 請遵循[如何在 Azure AD 中將 saml 型單一登入進行偵錯工具](../azuread-dev/howto-v1-debug-saml-sso-issues.md)教學課程，以瞭解如何捕捉 saml 要求。

1. 請連絡應用程式廠商並提供下列資訊︰

   -   SAML 要求

   -   [Azure AD 單一登入 SAML 通訊協定需求](../develop/single-sign-on-saml-protocol.md)

應用程式廠商應驗證其支援單一登入的 Azure AD SAML 執行。

## <a name="misconfigured-application"></a>應用程式設定錯誤

*錯誤 AADSTS650056：應用程式設定不正確。這可能是因為下列其中一個原因所致：用戶端未在用戶端的應用程式註冊中，以所要求的許可權列出 ' AAD Graph ' 的任何許可權。或者，系統管理員尚未同意租使用者。或者，檢查要求中的應用程式識別碼，以確保它符合所設定的用戶端應用程式識別碼。請洽詢您的系統管理員，以代表租使用者修正設定或同意*。

**可能的原因**

從應用程式傳送至 SAML 要求中 Azure AD 的 `Issuer` 屬性，與 Azure AD 中為應用程式所設定的識別碼值不相符。

**解決方案**

請確定 SAML 要求中的 `Issuer` 屬性符合 Azure AD 中設定的識別碼值。 如果您在 Azure 入口網站中使用具有我的應用程式安全瀏覽器擴充功能的[測試經驗](../azuread-dev/howto-v1-debug-saml-sso-issues.md)，則不需要手動執行下列步驟：

1.  開啟 [Azure 入口網站](https://portal.azure.com/)，然後以**全域管理員**或**共同管理員**身分登入。

1.  選取左邊主流覽功能表底部的 [**所有服務**]，以開啟 [ **Azure Active Directory] 延伸**模組。

1.  在篩選搜尋方塊中輸入 **"Azure Active Directory"** ，然後選取 [ **Azure Active Directory** ] 專案。

1.  從 Azure Active Directory 左側導覽功能表中選取 [**企業應用程式**]。

1.  選取 [所有應用程式]，以檢視所有應用程式的清單。

    若在這裡沒看到您要顯示的應用程式，請使用 [所有應用程式清單] 頂端的 [篩選] 控制項，並將 [顯示] 選項設定為 [所有應用程式]。

1.  選取您要設為單一登入的應用程式。

1.  在應用程式載入後，開啟 [基本 SAML 組態]。 確認 [識別碼] 文字方塊中的值符合錯誤中所顯示之識別碼值的值。


## <a name="certificate-or-key-not-configured"></a>未設定憑證或金鑰

*錯誤 AADSTS50003︰未設定簽署金鑰。*

**可能的原因**

應用程式物件已損毀，Azure AD 無法辨識為應用程式設定的憑證。

**解決方案**

若要刪除與建立新的憑證，請依照下列步驟執行：

1. 開啟 [Azure 入口網站](https://portal.azure.com/)，然後以**全域管理員**或**共同管理員**身分登入。

1. 按一下左側主導覽功能表底部的 [所有服務]，以開啟 [Azure Active Directory 延伸模組]。

1. 在篩選搜尋方塊中輸入 **"Azure Active Directory"** ，然後選取 [ **Azure Active Directory** ] 專案。

1. 從 Azure Active Directory 左側導覽功能表中選取 [**企業應用程式**]。

1. 選取 [所有應用程式]，以檢視所有應用程式的清單。

    若在這裡沒看到您要顯示的應用程式，請使用 [所有應用程式清單] 頂端的 [篩選] 控制項，並將 [顯示] 選項設定為 [所有應用程式]。

1. 選取您要設定單一登入的應用程式

1. 應用程式載入後，按一下應用程式的左側導覽功能表中的 [單一登入]。

1. 選取 [ **SAML 簽署憑證**] 區段下的 [**建立新憑證**]。

1. 選取 [到期日]，然後按一下 [**儲存**]。

1. 勾選 [Make new certificate active (啟用新憑證)] 以覆寫作用中的憑證。 然後按一下窗格頂端的 [儲存]，並按 [接受] 以啟動變換憑證。

1. 在 [SAML 簽署憑證] 區段下，按一下 [移除] 以移除**未使用**的憑證。

## <a name="saml-request-not-present-in-the-request"></a>要求中沒有 SAML 要求

*錯誤 AADSTS750054： SAMLRequest 或 SAMLResponse 必須在 SAML 重新導向系結的 HTTP 要求中顯示為查詢字串參數。*

**可能的原因**

Azure AD 無法識別 HTTP 要求中 URL 參數內的 SAML 要求。 如果在將 SAML 要求傳送至 Azure AD 時，應用程式未使用 HTTP 重新導向系結，就會發生這種情況。

**解決方案**

應用程式必須使用 HTTP 重新導向系結，將編碼的 SAML 要求傳送至位置標頭。 如需有關如何進行此實作的詳細資訊，請參閱 [SAML 通訊協定規格文件](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf) \(英文\) 中的＜HTTP 重新導向繫結＞一節。

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>Azure AD 正在將權杖傳送至不正確的端點

**可能的原因**

在單一登入期間，如果登入要求不包含明確的回復 URL （判斷提示取用者服務 URL），則 Azure AD 會為該應用程式選取任何已設定的「依賴 Url」。 即使應用程式已設定明確的回復 URL，使用者仍可能被重新導向 https://127.0.0.1:444。 

當應用程式已新增為非資源庫應用程式時，Azure Active Directory 就會建立此回覆 URL 做為預設值。 此行為已變更，Azure Active Directory 預設不會再新增此 URL。 

**解決方案**

刪除為應用程式設定的未使用的回復 Url。

1.  開啟 [Azure 入口網站](https://portal.azure.com/)，然後以**全域管理員**或**共同管理員**身分登入。

2.  選取左邊主流覽功能表底部的 [**所有服務**]，以開啟 [ **Azure Active Directory] 延伸**模組。

3.  在篩選搜尋方塊中輸入 **"Azure Active Directory"** ，然後選取 [ **Azure Active Directory** ] 專案。

4.  從 Azure Active Directory 左側導覽功能表中選取 [**企業應用程式**]。

5.  選取 [所有應用程式]，以檢視所有應用程式的清單。

    若在這裡沒看到您要顯示的應用程式，請使用 [所有應用程式清單] 頂端的 [篩選] 控制項，並將 [顯示] 選項設定為 [所有應用程式]。

6.  選取您要設為單一登入的應用程式。

7.  在應用程式載入後，開啟 [基本 SAML 組態]。 在 [**回復 URL （判斷提示取用者服務 URL）** ] 中，刪除系統所建立的未使用或預設回復 url。 例如： `https://127.0.0.1:444/applications/default.aspx` 。

## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>自訂傳送至應用程式的 SAML 宣告時發生問題

若要瞭解如何自訂傳送至應用程式的 SAML 屬性宣告，請參閱[Azure Active Directory 中的宣告對應](../develop/active-directory-claims-mapping.md)。

## <a name="next-steps"></a>後續步驟

[如何偵錯 SAML 型單一登入 Azure Active Directory 中的應用程式](../azuread-dev/howto-v1-debug-saml-sso-issues.md)

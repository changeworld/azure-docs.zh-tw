---
title: 登錄聯合單一登入庫應用時遇到問題 |微軟文檔
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
ms.openlocfilehash: 874d273e26a728afc0a1dc1a16852016797067ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77367894"
---
# <a name="problems-signing-in-to-a-gallery-application-configured-for-federated-single-sign-on"></a>登入針對同盟單一登入設定之資源庫應用程式的問題

要解決下面的登錄問題，我們建議您遵循以下建議，以獲得更好的診斷並自動執行解決步驟：

- 安裝["我的應用安全瀏覽器延伸](access-panel-extension-problem-installing.md)"，以説明 Azure 活動目錄 （Azure AD） 在 Azure 門戶中使用測試體驗時提供更好的診斷和解決方法。
- 使用 Azure 門戶中應用配置頁中的測試體驗重現錯誤。 瞭解有關基於[SAML 的調試單登錄應用程式](../azuread-dev/howto-v1-debug-saml-sso-issues.md)的更多資訊


## <a name="application-not-found-in-directory"></a>在目錄中找不到應用程式

*錯誤 AADSTS70001： 在目錄中找不到具有識別碼\/"HTTPs： /contoso.com"的應用程式*。

**可能的原因**

在`Issuer`SAML 請求中從應用程式發送到 Azure AD 的屬性與 Azure AD 中為應用程式佈建的識別碼值不匹配。

**解析度**

確保 SAML 請求中`Issuer`的屬性與 Azure AD 中配置的識別碼值匹配。 如果在 Azure 門戶中使用"我的應用安全瀏覽器延伸"中的[測試體驗](../azuread-dev/howto-v1-debug-saml-sso-issues.md)，則無需手動執行這些步驟。

1.  打開[**Azure 門戶**](https://portal.azure.com/)，以**全域管理員**或**共同管理員**身份登錄。

1.  通過選擇主左側導航功能表頂部**的所有服務**，打開**Azure 活動目錄擴展。**

1.  在篩選器搜索框中鍵入 **"Azure 活動目錄"，** 然後選擇**Azure 活動目錄**項。

1.  從 Azure 活動目錄左側導航功能表中選擇**企業應用程式**。

1.  選擇 **"所有應用程式**"以查看所有應用程式的清單。

    如果您沒有看到要在此處顯示的應用程式，請使用 **"所有應用程式清單**"頂部的 **"篩選器"** 控制項，並將 **"顯示**"選項設置為 **"所有應用程式**"。

1.  選取您要設為單一登入的應用程式。

1.  在應用程式載入後，開啟 [基本 SAML 組態]****。 驗證識別碼文字方塊中的值是否與錯誤中顯示的識別碼值的值匹配。



## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>答覆位址與為應用程式佈建的回復位址不匹配

*錯誤 AADSTS50011：回復位址"HTTPs：/contoso.com"\/與為應用程式佈建的回復位址不匹配*

**可能的原因**

SAML 請求中`AssertionConsumerServiceURL`的值與 Azure AD 中配置的"回復 URL"值或模式不匹配。 SAML 請求中`AssertionConsumerServiceURL`的值是您在錯誤中看到的 URL。

**解析度**

確保 SAML 請求中`AssertionConsumerServiceURL`的值與 Azure AD 中配置的"回復 URL"值匹配。 如果在 Azure 門戶中使用"我的應用安全瀏覽器延伸"中的[測試體驗](../azuread-dev/howto-v1-debug-saml-sso-issues.md)，則無需手動執行這些步驟。

1.  打開[**Azure 門戶**](https://portal.azure.com/)，以**全域管理員**或**共同管理員**身份登錄。

1.  通過選擇主左側導航功能表頂部**的所有服務**，打開**Azure 活動目錄擴展。**

1.  在篩選器搜索框中鍵入 **"Azure 活動目錄"，** 然後選擇**Azure 活動目錄**項。

1.  從 Azure 活動目錄左側導航功能表中選擇**企業應用程式**。

1.  選擇 **"所有應用程式**"以查看所有應用程式的清單。

    如果您沒有看到要在此處顯示的應用程式，請使用 **"所有應用程式清單**"頂部的 **"篩選器"** 控制項，並將 **"顯示**"選項設置為 **"所有應用程式**"。

1.  選取您要設為單一登入的應用程式。

1.  在應用程式載入後，開啟 [基本 SAML 組態]****。 驗證或更新"回復 URL"文字方塊中的值，以匹配`AssertionConsumerServiceURL`SAML 請求中的值。    
    
更新 Azure AD 中的"回復 URL"值後，該值與應用程式在 SAML 請求中發送的值匹配，您應該能夠登錄到應用程式。

## <a name="user-not-assigned-a-role"></a>使用者未指派角色

*錯誤 AADSTS50105： 登錄的使用者"brian\@contoso.com"不會分配給應用程式的角色*。

**可能的原因**

尚未將 Azure AD 中應用程式的存取權授與使用者。

**解析度**

要將一個或多個使用者直接分配給應用程式，請按照以下步驟操作。 如果在 Azure 門戶中使用"我的應用安全瀏覽器延伸"中的[測試體驗](../azuread-dev/howto-v1-debug-saml-sso-issues.md)，則無需手動執行這些步驟。

1.  打開[**Azure 門戶**](https://portal.azure.com/)並作為**全域管理員**登錄。

1.  通過選擇主左側導航功能表頂部**的所有服務**，打開**Azure 活動目錄擴展。**

1.  在篩選器搜索框中鍵入 **"Azure 活動目錄**"，然後選擇**Azure 活動目錄**項。

1.  從 Azure 活動目錄左側導航功能表中選擇**企業應用程式**。

1.  選擇 **"所有應用程式**"以查看所有應用程式的清單。

    如果您沒有看到要在此處顯示的應用程式，請使用 **"所有應用程式清單**"頂部的 **"篩選器"** 控制項，並將 **"顯示**"選項設置為 **"所有應用程式**"。

1.  從應用程式清單中選擇要將使用者分配給的應用程式。

1.  載入應用程式後，從應用程式的左側導航功能表中選擇 **"使用者"和"組**"。

1.  按一下 [使用者和群組]**** 清單頂端的 [新增]**** 按鈕，以開啟 [新增指派]**** 窗格。

1.  從"**添加分配**"窗格中選擇 **"使用者"和"組**"選擇器。

1. 在 [依名稱或電子郵件地址搜尋]**** 搜尋方塊中，針對您要新增的使用者輸入其完整名稱或電子郵件地址。

1. 將滑鼠懸停在清單中**的使用者**上以顯示**核取方塊**。 按一下使用者個人資料照片或徽標旁邊的核取方塊，將使用者添加到 **"選定"** 清單。

1. **可選：** 如果要**添加多個使用者**，請在 **"按姓名或電子郵件地址搜索"** 框中鍵入另一個全名或電子郵件地址，然後按一下該核取方塊將使用者添加到 **"選定"** 清單。

1. 選擇完使用者後，按一下 **"選擇**"按鈕將其添加到要分配給應用程式的使用者和組清單中。

1. **可選：** 按一下"**添加分配"** 窗格中的 **"選擇角色**選擇器"以選擇要分配給所選使用者的角色。

1. 按一下"**分配"** 按鈕將應用程式分配給選定的使用者。

經過一段時間後，您選擇的使用者將能夠使用解決方案描述部分中描述的方法啟動這些應用程式。

## <a name="not-a-valid-saml-request"></a>不是有效的 SAML 請求

*錯誤 AADSTS75005：要求不是有效的 Saml2 通訊協定訊息。*

**可能的原因**

Azure AD 不支援應用程式為單一登入傳送的 SAML 要求。 以下為一些常見問題：

-   SAML 要求中遺漏必要的欄位
-   SAML 要求編碼方法

**解析度**

1. 捕獲 SAML 請求。 請按照教程["如何調試 Azure AD 中基於 SAML 的單一登入](../azuread-dev/howto-v1-debug-saml-sso-issues.md)"來瞭解如何捕獲 SAML 請求。

1. 請連絡應用程式廠商並提供下列資訊︰

   -   SAML 要求

   -   [Azure AD 單一登入 SAML 通訊協定需求](../develop/single-sign-on-saml-protocol.md)

應用程式供應商應驗證他們是否支援單登錄的 Azure AD SAML 實現。

## <a name="misconfigured-application"></a>配置錯誤的應用程式

*錯誤 AADSTS650056：配置錯誤的應用程式。這可能是由於以下原因之一：用戶端未在用戶端的應用程式註冊中列出請求的許可權中的任何許可權。或者，管理員尚未同意在租戶中。或者，檢查請求中的應用程式識別碼，以確保它與配置的用戶端應用程式識別碼匹配。請與您的管理員聯繫，以代表租戶修復配置或同意。*

**可能的原因**

在`Issuer`SAML 請求中從應用程式發送到 Azure AD 的屬性與 Azure AD 中為應用程式佈建的識別碼值不匹配。

**解析度**

確保 SAML 請求中`Issuer`的屬性與 Azure AD 中配置的識別碼值匹配。 如果在 Azure 門戶中使用"我的應用安全瀏覽器延伸"中的[測試體驗](../azuread-dev/howto-v1-debug-saml-sso-issues.md)，則無需手動執行以下步驟：

1.  打開[**Azure 門戶**](https://portal.azure.com/)，以**全域管理員**或**共同管理員**身份登錄。

1.  通過選擇主左側導航功能表頂部**的所有服務**，打開**Azure 活動目錄擴展。**

1.  在篩選器搜索框中鍵入 **"Azure 活動目錄"，** 然後選擇**Azure 活動目錄**項。

1.  從 Azure 活動目錄左側導航功能表中選擇**企業應用程式**。

1.  選擇 **"所有應用程式**"以查看所有應用程式的清單。

    如果您沒有看到要在此處顯示的應用程式，請使用 **"所有應用程式清單**"頂部的 **"篩選器"** 控制項，並將 **"顯示**"選項設置為 **"所有應用程式**"。

1.  選取您要設為單一登入的應用程式。

1.  在應用程式載入後，開啟 [基本 SAML 組態]****。 驗證識別碼文字方塊中的值是否與錯誤中顯示的識別碼值的值匹配。


## <a name="certificate-or-key-not-configured"></a>未設定憑證或金鑰

*錯誤 AADSTS50003︰未設定簽署金鑰。*

**可能的原因**

應用程式物件已損毀，Azure AD 無法辨識為應用程式設定的憑證。

**解析度**

若要刪除與建立新的憑證，請依照下列步驟執行：

1. 打開[**Azure 門戶**](https://portal.azure.com/)，以**全域管理員**或**共同管理員**身份登錄。

1. 按一下左側主導覽功能表底部的 [所有服務]****，以開啟 [Azure Active Directory 延伸模組]****。

1. 在篩選器搜索框中鍵入 **"Azure 活動目錄"，** 然後選擇**Azure 活動目錄**項。

1. 從 Azure 活動目錄左側導航功能表中選擇**企業應用程式**。

1. 選擇 **"所有應用程式**"以查看所有應用程式的清單。

    如果您沒有看到要在此處顯示的應用程式，請使用 **"所有應用程式清單**"頂部的 **"篩選器"** 控制項，並將 **"顯示**"選項設置為 **"所有應用程式**"。

1. 選取您要設定單一登入的應用程式

1. 應用程式載入後，按一下應用程式的左側導覽功能表中的 [單一登入]****。

1. 選擇在**SAML 簽章憑證**部分下**創建新證書**。

1. 選擇到期日期，然後按一下"**保存**"。

1. 選中**使新證書處於活動狀態**以覆蓋活動證書。 然後按一下窗格頂端的 [儲存]****，並按 [接受] 以啟動變換憑證。

1. 在 [SAML 簽署憑證] 區段下，按一下 [移除] 以移除**********未使用**的憑證。

## <a name="saml-request-not-present-in-the-request"></a>要求中沒有 SAML 要求

*錯誤 AADSTS750054： SAML請求或 SAML 回應必須作為查詢字串參數在 SAML 重定向綁定的 HTTP 要求中存在。*

**可能的原因**

Azure AD 無法識別 HTTP 要求中 URL 參數內的 SAML 要求。 如果應用程式在將 SAML 要求傳送到 Azure AD 時未使用 HTTP 重定向綁定，則可能發生此情況。

**解析度**

應用程式需要使用 HTTP 重定向綁定將編碼到位置標頭中的 SAML 要求傳送。 如需有關如何進行此實作的詳細資訊，請參閱 [SAML 通訊協定規格文件](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf) \(英文\) 中的＜HTTP 重新導向繫結＞一節。

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>Azure AD 將權杖發送到不正確的終結點

**可能的原因**

在單次登錄期間，如果登錄請求不包含顯式回復 URL（聲明消費者服務 URL），則 Azure AD 將為該應用程式選擇任何配置的依賴 URL。 即使應用程式佈建了顯式答覆 URL，使用者也可能重定向https://127.0.0.1:444。 

當應用程式已新增為非資源庫應用程式時，Azure Active Directory 就會建立此回覆 URL 做為預設值。 此行為已變更，Azure Active Directory 預設不會再新增此 URL。 

**解析度**

刪除為應用程式佈建的未使用的回復 URL。

1.  打開[**Azure 門戶**](https://portal.azure.com/)，以**全域管理員**或**共同管理員**身份登錄。

2.  通過選擇主左側導航功能表頂部**的所有服務**，打開**Azure 活動目錄擴展。**

3.  在篩選器搜索框中鍵入 **"Azure 活動目錄"，** 然後選擇**Azure 活動目錄**項。

4.  從 Azure 活動目錄左側導航功能表中選擇**企業應用程式**。

5.  選擇 **"所有應用程式**"以查看所有應用程式的清單。

    如果您沒有看到要在此處顯示的應用程式，請使用 **"所有應用程式清單**"頂部的 **"篩選器"** 控制項，並將 **"顯示**"選項設置為 **"所有應用程式**"。

6.  選取您要設為單一登入的應用程式。

7.  在應用程式載入後，開啟 [基本 SAML 組態]****。 在**回復 URL（聲明消費者服務 URL）中，** 刪除系統創建的未使用或預設的回復 URL。 例如： `https://127.0.0.1:444/applications/default.aspx` 。

## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>自訂傳送至應用程式的 SAML 宣告時發生問題

要瞭解如何自訂發送到應用程式的 SAML 屬性聲明，請參閱 Azure[活動目錄中的宣告對應](../develop/active-directory-claims-mapping.md)。

## <a name="next-steps"></a>後續步驟

[如何偵錯 SAML 型單一登入 Azure Active Directory 中的應用程式](../azuread-dev/howto-v1-debug-saml-sso-issues.md)

---
title: 頁面配置版本
titleSuffix: Azure AD B2C
description: 自訂原則中的 UI 自訂頁面配置版本歷程記錄。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/24/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 868d99a82009dc8545fc24ad1cfa1da3959da131
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88852081"
---
# <a name="page-layout-versions"></a>頁面配置版本

頁面配置套件會定期更新，以包含其頁面元素的修正和增強功能。 下列變更記錄檔會指定每個版本中所引進的變更。

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="self-asserted-page-selfasserted"></a>自我判斷頁面 (selfasserted) 

**2.1.0**

- 當地語系化和協助工具修正。

**2.0.0**

- 已新增自訂原則中的 [顯示控制項](display-controls.md) 支援。

**1.2.0**

- 使用者名稱/電子郵件和密碼欄位現在會使用 `form` HTML 元素，以允許 Edge 和 Internet Explorer (IE) 適當地儲存這項資訊。
- 新增可設定的使用者輸入驗證延遲，以改善使用者體驗。
-  協助工具修正
- 您現在可以 `data-preload="true"` [在 HTML 標籤中](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) 加入屬性，以控制 CSS 和 JavaScript 的載入順序。
  - 同時載入連結的 CSS 檔案與您的 HTML 範本，使其不會在載入檔案時「閃爍」。
  - 控制在 `script` 頁面載入之前提取和執行標記的順序。
- 電子郵件欄位現在是 `type=email` ，行動鍵盤會提供正確的建議
- 支援 Chrome 轉譯

**1.1.0**

- 已移除取消警示
- Error 元素的 CSS 類別
- 顯示/隱藏錯誤邏輯已改進
- 預設 CSS 已移除

**1.0.0**

- 初始版本

## <a name="unified-sign-in-sign-up-page-with-password-reset-link-unifiedssp"></a>具有密碼重設連結 (unifiedssp) 的整合式登入註冊頁面

**2.1.0**

- 新增多個註冊連結的支援。
- 已根據原則中定義的述詞規則，新增使用者輸入驗證的支援。

**1.2.0**

- 使用者名稱/電子郵件和密碼欄位現在會使用 `form` HTML 元素，以允許 Edge 和 Internet Explorer (IE) 適當地儲存這項資訊。
-  協助工具修正
- 您現在可以 `data-preload="true"` [在 HTML 標籤中](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) 加入屬性，以控制 CSS 和 JavaScript 的載入順序。
  - 同時載入連結的 CSS 檔案與您的 HTML 範本，使其不會在載入檔案時「閃爍」。
  - 控制在 `script` 頁面載入之前提取和執行標記的順序。
- 電子郵件欄位現在是 `type=email` ，行動鍵盤會提供正確的建議
- 支援 Chrome 轉譯

**1.1.0**

- 已新增 (KMSI) 控制項讓我保持登入

**1.0.0**

- 初始版本

## <a name="mfa-page-multifactor"></a>MFA 頁面 (多因素) 

**1.2.1**

- 預設範本的協助工具修正

**1.2.0**

-  協助工具修正
- 您現在可以 `data-preload="true"` [在 HTML 標籤中](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) 加入屬性，以控制 CSS 和 JavaScript 的載入順序。
  - 同時載入連結的 CSS 檔案與您的 HTML 範本，使其不會在載入檔案時「閃爍」。
  - 控制在 `script` 頁面載入之前提取和執行標記的順序。
- 電子郵件欄位現在是 `type=email` ，行動鍵盤會提供正確的建議
- 支援 Chrome 轉譯

**1.1.0**

- 已移除 [確認程式碼] 按鈕
- 程式碼的輸入欄位現在只會接受最多六個 (6) 字元的輸入
- 頁面會自動嘗試驗證輸入6位數代碼時所輸入的程式碼，而不需要按一下任何按鈕
- 如果程式碼錯誤，則會自動清除輸入欄位
- 在三 (3) 嘗試使用不正確的程式碼時，B2C 會將錯誤傳回給信賴憑證者
-  協助工具修正
- 預設 CSS 已移除

**1.0.0**

- 初始版本

## <a name="exception-page-globalexception"></a>例外狀況頁面 (globalexception) 

**1.2.0**

-  協助工具修正
- 您現在可以 `data-preload="true"` [在 HTML 標籤中](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) 加入屬性，以控制 CSS 和 JavaScript 的載入順序。
  - 同時載入連結的 CSS 檔案與您的 HTML 範本，使其不會在載入檔案時「閃爍」。
  - 控制在 `script` 頁面載入之前提取和執行標記的順序。
- 電子郵件欄位現在是 `type=email` ，行動鍵盤會提供正確的建議
- 支援 Chrome 轉譯

**1.1.0**

- 協助工具修正
- 從原則中沒有任何連絡人時移除預設訊息
- 預設 CSS 已移除

**1.0.0**

- 初始版本

## <a name="other-pages-providerselection-claimsconsent-unifiedssd"></a>其他頁面 (ProviderSelection、ClaimsConsent、UnifiedSSD) 

**1.2.0**

-  協助工具修正
- 您現在可以 `data-preload="true"` [在 HTML 標籤中](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) 加入屬性，以控制 CSS 和 JavaScript 的載入順序。
  - 同時載入連結的 CSS 檔案與您的 HTML 範本，使其不會在載入檔案時「閃爍」。
  - 控制在 `script` 頁面載入之前提取和執行標記的順序。
- 電子郵件欄位現在是 `type=email` ，行動鍵盤會提供正確的建議
- 支援 Chrome 轉譯

**1.0.0**

- 初始版本

## <a name="next-steps"></a>後續步驟

如需如何在自訂原則中自訂應用程式使用者介面的詳細資訊，請參閱 [使用自訂原則來自訂應用程式的使用者介面](custom-policy-ui-customization.md)。

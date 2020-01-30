---
title: 選取頁面配置-Azure Active Directory B2C
description: 瞭解如何在 Azure Active Directory B2C 中選取頁面配置。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 452687f3886a85bea796e3899410667ee1d592fa
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840310"
---
# <a name="select-a-page-layout-in-azure-active-directory-b2c-using-custom-policies"></a>使用自訂原則在 Azure Active Directory B2C 中選取頁面配置

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

無論您使用的是使用者流程或自訂原則，您都可以在 Azure Active Directory B2C （Azure AD B2C）原則中啟用 JavaScript 用戶端程式代碼。 若要為您的應用程式啟用 JavaScript，您必須將元素新增至您的[自訂原則](custom-policy-overview.md)、選取頁面配置，然後在您的要求中使用[b2clogin.com](b2clogin.md) 。

頁面配置是 Azure AD B2C 提供的專案與您提供的內容的關聯。

本文討論如何藉由在自訂原則中設定頁面配置，在 Azure AD B2C 中選取該版面配置。

> [!NOTE]
> 如果您想要為使用者流程啟用 JavaScript，請參閱[Azure Active Directory B2C 中的 javascript 和頁面配置版本](user-flow-javascript-overview.md)。

## <a name="replace-datauri-values"></a>取代 DataUri 值

在您的自訂原則中，您可能會有定義用於使用者旅程圖中之 HTML 範本的 [ContentDefinitions](contentdefinitions.md)。 **ContentDefinition** 包含會參考由 Azure AD B2C 所提供之頁面元素的 **DataUri**。 **LoadUri** 是針對您提供之 HTML 和 CSS 內容的相對路徑。

```XML
<ContentDefinition Id="api.idpselections">
  <LoadUri>~/tenant/default/idpSelector.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Idp selection page</Item>
    <Item Key="language.intro">Sign in</Item>
  </Metadata>
</ContentDefinition>
```

若要選取頁面配置，您可以在原則中變更[ContentDefinitions](contentdefinitions.md)中的**DataUri**值。 藉由從舊的 **DataUri** 值切換到新的值，您將會選取不可變的套件。 使用此套件的優點是您知道它不會變更，而且會在您的頁面上造成非預期的行為。

若要在使用舊**DataUri**值的自訂原則中指定頁面配置，請在 `elements` 和頁面類型之間插入 `contract` （例如 `selfasserted`），並指定版本號碼。 例如：

| 舊的 DataUri 值 | 新的 DataUri 值 |
| ----------------- | ----------------- |
| `urn:com:microsoft:aad:b2c:elements:claimsconsent:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:claimsconsent:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.1.0` |

## <a name="version-change-log"></a>版本變更記錄檔

頁面配置套件會定期更新，以在其頁面元素中包含修正和改善。 下列變更記錄檔指定每個版本中引進的變更。

### <a name="200"></a>2.0.0

- 自我判斷頁（`selfasserted`）
  - 已新增自訂原則中的[顯示控制項](display-controls.md)支援。

### <a name="120"></a>1.2.0

- 所有頁面
  - 協助工具修正程式
  - 您現在可以在 HTML 標籤中加入 `data-preload="true"` 屬性，以控制 CSS 和 JavaScript 的載入順序。 案例包括：
    - 在您的 CSS 連結上使用這種方式，同時載入 CSS 與 HTML，使其不會在載入檔案時「閃爍」
    - 這個屬性可讓您控制在頁面載入之前提取和執行腳本標記的順序
  - [電子郵件] 欄位現在已 `type=email`，而行動電話鍵盤會提供正確的建議
  - 支援 Chrome 轉譯
- 統一且自我判斷的頁面
  - [使用者名稱/電子郵件] 和 [密碼] 欄位現在會使用表單 HTML 元素。  這現在會允許 Edge 和 IE 適當地儲存此資訊

### <a name="110"></a>1.1.0

- 例外狀況頁面（globalexception）
  - 協助工具修正
  - 當原則沒有任何連絡人時，移除預設訊息
  - 已移除預設 CSS
- MFA 頁面（多重要素）
  - 已移除 [確認碼] 按鈕
  - 程式碼的輸入欄位現在只接受最多6個字元的輸入（6）
  - 當輸入6位數代碼時，網頁會自動嘗試驗證輸入的程式碼，而不需要按一下任何按鈕
  - 如果程式碼錯誤，則會自動清除輸入欄位
  - 在三（3）次嘗試使用不正確的程式碼之後，B2C 會將錯誤傳回給信賴憑證者
  - 協助工具修正程式
  - 已移除預設 CSS
- 自我判斷頁（selfasserted）
  - 已移除取消警示
  - Error 元素的 CSS 類別
  - 顯示/隱藏已改善的錯誤邏輯
  - 已移除預設 CSS
- 統一的 SSP （unifiedssp）
  - 新增 [讓我保持登入（KMSI）] 控制項

### <a name="100"></a>1.0.0

- 初始版本

## <a name="next-steps"></a>後續步驟

如需如何自訂應用程式之使用者介面的詳細資訊，請參閱[在 Azure Active Directory B2C 中使用自訂原則來自訂應用程式的使用者介面](custom-policy-ui-customization.md)。

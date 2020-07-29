---
title: 頁面配置版本
titleSuffix: Azure AD B2C
description: 自訂原則中 UI 自訂的頁面配置版本歷程記錄。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3d0cb06f84fdd96d099e05f55ba62c37cb1192c7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "78183970"
---
# <a name="page-layout-versions"></a>頁面配置版本

頁面配置套件會定期更新，以在其頁面元素中包含修正和改善。 下列變更記錄檔指定每個版本中引進的變更。

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="200"></a>2.0.0

- 自我判斷頁（ `selfasserted` ）
  - 已新增自訂原則中的[顯示控制項](display-controls.md)支援。

## <a name="120"></a>1.2.0

- 所有頁面
  -  協助工具修正
  - 您現在可以 `data-preload="true"` [在 HTML 標籤中](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content)加入屬性，以控制 CSS 和 JavaScript 的載入順序。
    - 將連結的 CSS 檔案與您的 HTML 範本同時載入，使其不會在載入檔案時「閃爍」。
    - 控制在 `script` 頁面載入之前提取和執行標記的順序。
  - [電子郵件] 欄位現在是 `type=email` ，而行動電話鍵盤會提供正確的建議
  - 支援 Chrome 轉譯
- 統一和自我判斷的頁面
  - [使用者名稱/電子郵件] 和 [密碼] 欄位現在會使用 `form` HTML 元素，讓 Edge 和 Internet Explorer （IE）適當地儲存此資訊。

## <a name="110"></a>1.1.0

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
  -  協助工具修正
  - 已移除預設 CSS
- 自我判斷頁（selfasserted）
  - 已移除取消警示
  - Error 元素的 CSS 類別
  - 顯示/隱藏已改善的錯誤邏輯
  - 已移除預設 CSS
- 統一的 SSP （unifiedssp）
  - 新增 [讓我保持登入（KMSI）] 控制項

## <a name="100"></a>1.0.0

- 初始版本

## <a name="next-steps"></a>後續步驟

如需如何在自訂原則中自訂應用程式使用者介面的詳細資訊，請參閱[使用自訂原則自訂應用程式的使用者介面](custom-policy-ui-customization.md)。

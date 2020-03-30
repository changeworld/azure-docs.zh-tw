---
title: 頁面配置版本
titleSuffix: Azure AD B2C
description: 用於自訂策略中的 UI 自訂的頁面配置版本歷程記錄。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183970"
---
# <a name="page-layout-versions"></a>頁面配置版本

頁面配置包會定期更新，以在其頁面元素中包含修復和改進。 以下更改日誌指定每個版本中引入的更改。

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="200"></a>2.0.0

- 自斷言頁 （`selfasserted`）
  - 在自訂策略中添加了對[顯示控制項](display-controls.md)的支援。

## <a name="120"></a>1.2.0

- 所有頁面
  -  協助工具修正
  - 現在，`data-preload="true"`您可以在 HTML[標記中](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content)添加該屬性，以控制 CSS 和 JavaScript 的載入順序。
    - 與 HTML 範本同時載入連結的 CSS 檔，這樣不會在載入檔之間"閃爍"。
    - 控制標記在`script`頁面載入之前提取和執行的順序。
  - 電子郵件欄位是現在`type=email`和移動鍵盤將提供正確的建議
  - 支援 Chrome 翻譯
- 統一和自斷言頁面
  - 使用者名/電子郵件和密碼欄位現在使用`form`HTML 元素允許邊緣和 Internet 資源管理器 （IE） 正確保存此資訊。

## <a name="110"></a>1.1.0

- 異常頁（全域異常）
  - 協助工具修復
  - 當策略沒有連絡人時，已刪除預設消息
  - 已刪除預設 CSS
- MFA 頁面（多因素）
  - "確認代碼"按鈕已刪除
  - 代碼的輸入欄位現在最多隻能輸入六 （6） 個字元
  - 頁面將自動嘗試驗證輸入 6 位代碼時輸入的代碼，而無需按一下任何按鈕
  - 如果代碼錯誤，將自動清除輸入欄位
  - 在三 （3） 次嘗試代碼不正確後，B2C 會將錯誤發送回依賴方
  -  協助工具修正
  - 已刪除預設 CSS
- 自斷言頁面（自斷言）
  - 已刪除取消警報
  - 用於錯誤元素的 CSS 類
  - 顯示/隱藏錯誤邏輯改進
  - 已刪除預設 CSS
- 統一 SSP（統一）
  - 已添加保持登錄 （KMSI） 控制

## <a name="100"></a>1.0.0

- 初始版本

## <a name="next-steps"></a>後續步驟

有關如何在自訂策略中自訂應用程式的使用者介面的詳細資訊，請參閱[使用自訂策略自訂應用程式的使用者介面](custom-policy-ui-customization.md)。

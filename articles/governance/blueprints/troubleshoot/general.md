---
title: 常見問題疑難排解
description: 瞭解如何針對建立、指派和移除藍圖的問題進行疑難排解，例如原則違規和藍圖參數功能。
ms.date: 06/29/2020
ms.topic: troubleshooting
ms.openlocfilehash: e8362e2a22317d73e0fd392bd497cd9f2c5ffe4f
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89651323"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>針對使用 Azure 藍圖發生的錯誤進行疑難排解

您可能會在建立、指派或移除藍圖時遇到錯誤。 此文章說明可能發生的各種錯誤與解決方式。

## <a name="finding-error-details"></a>尋找錯誤詳細資料

許多錯誤都是將藍圖指派給某個範圍而產生的結果。 當指派失敗時，藍圖會提供有關失敗部署的詳細資料。 此資訊會指出問題，以便能夠進行修正，而使下一次部署能成功。

1. 在左側窗格中選取 [所有服務]  。 搜尋並選取 [藍圖]  。

1. 從左側頁面選取 [ **指派的藍圖** ]，然後使用 [搜尋] 方塊來篩選藍圖指派，以找出失敗的指派。 您也可以依 [佈建狀態]**** 欄排序指派表格，以查看所有已群組在一起的失敗指派。

1. 選取狀態為 [ _失敗_ ] 的藍圖，或按一下滑鼠右鍵並選取 [ **視圖指派詳細資料**]。

1. 代表指派失敗的紅色橫幅警告位於藍圖指派頁面的最上方。 選取橫幅上的任何位置，以取得更多詳細資料。

錯誤通常是由成品 (而非整個藍圖) 造成的。 如果成品建立金鑰保存庫，而且 Azure 原則防止建立金鑰保存庫，則整個指派會失敗。

## <a name="general-errors"></a>一般錯誤

### <a name="scenario-policy-violation"></a><a name="policy-violation"></a>案例：違反原則

#### <a name="issue"></a>問題

範本部署因為原則違規而失敗。

#### <a name="cause"></a>原因

原則可能會因為多種因素而與部署產生衝突：

- 要建立的資源受到原則所限制 (通常是 SKU 或位置限制)
- 部署正在設定原則所設定的欄位 (通常會使用標記)

#### <a name="resolution"></a>解決方案

變更藍圖，使其不會與錯誤詳細資料中的原則發生發生衝突。 如果此變更不可行，替代選項就是變更原則指派的範圍，使藍圖不再與原則產生衝突。

### <a name="scenario-blueprint-parameter-is-a-function"></a><a name="escape-function-parameter"></a>案例：藍圖參數是函式

#### <a name="issue"></a>問題

為函式的藍圖參數會先經過處理，再傳遞至成品。

#### <a name="cause"></a>原因

將使用如 `[resourceGroup().tags.myTag]` 等函式的藍圖參數傳遞至成品，會造成在成品上設定該函式處理過後的結果，而非動態函式。

#### <a name="resolution"></a>解決方案

若要將函式作為參數傳遞，請使用 `[` 逸出整個字串，讓藍圖參數看起來像 `[[resourceGroup().tags.myTag]`。 逸出字元會導致「藍圖」在處理藍圖時，將值視為字串。 接著「藍圖」會將函式置於成品上，讓它如預期般地為動態函式。 如需詳細資訊，請參閱 [Azure Resource Manager 範本中的語法和運算式](../../../azure-resource-manager/templates/template-expressions.md)。

## <a name="delete-errors"></a>刪除錯誤

### <a name="scenario-assignment-deletion-timeout"></a><a name="assign-delete-timeout"></a>案例：指派刪除超時

#### <a name="issue"></a>問題

刪除藍圖指派並不會完成。

#### <a name="cause"></a>原因

藍圖指派在刪除時可能會卡在非終端狀態。 當藍圖指派建立的資源仍在擱置中刪除，或未將狀態碼傳回 Azure 藍圖時，就會發生此狀態。

#### <a name="resolution"></a>解決方案

處於非終端機狀態的藍圖指派會在_6 小時_的時間內自動標示為**失敗**。 一旦 timeout 調整了藍圖指派的狀態，就可以重試刪除。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

- 透過 [Azure 論壇](https://azure.microsoft.com/support/forums/)獲得由 Azure 專家所提供的解答。
- 與 [@AzureSupport](https://twitter.com/azuresupport) 連繫－專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，像是解答、支援及專家等。
- 如果需要更多協助，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。
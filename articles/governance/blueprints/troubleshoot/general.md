---
title: 常見問題疑難排解
description: 瞭解如何解決創建、分配和刪除藍圖（如策略違規和藍圖參數函數）的問題。
ms.date: 01/15/2020
ms.topic: troubleshooting
ms.openlocfilehash: 7306e344a479008a87164a954c4444d375950b0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157078"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>針對使用 Azure 藍圖發生的錯誤進行疑難排解

創建、分配或刪除藍圖時可能會遇到錯誤。 此文章說明可能發生的各種錯誤與解決方式。

## <a name="finding-error-details"></a>尋找錯誤詳細資料

許多錯誤都是將藍圖指派給某個範圍而產生的結果。 當指派失敗時，藍圖會提供有關失敗部署的詳細資料。 此資訊會指出問題，以便能夠進行修正，而使下一次部署能成功。

1. 在左側窗格中選取 [所有服務]****。 搜尋並選取 [藍圖]****。

1. 從左側的頁面選擇 **"已分配的藍圖**"，並使用搜索框篩選藍圖分配以查找失敗的分配。 您也可以依 [佈建狀態]**** 欄排序指派表格，以查看所有已群組在一起的失敗指派。

1. 左鍵按一下具有 _"失敗"_ 狀態的藍圖或按右鍵並選擇 **"查看分配詳細資訊**"。

1. 代表指派失敗的紅色橫幅警告位於藍圖指派頁面的最上方。 按一下該橫幅上的任一處，以取得更多詳細資料。

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

### <a name="scenario-blueprint-parameter-is-a-function"></a><a name="escape-function-parameter"></a>方案：藍圖參數是函數

#### <a name="issue"></a>問題

為函式的藍圖參數會先經過處理，再傳遞至成品。

#### <a name="cause"></a>原因

將使用如 `[resourceGroup().tags.myTag]` 等函式的藍圖參數傳遞至成品，會造成在成品上設定該函式處理過後的結果，而非動態函式。

#### <a name="resolution"></a>解決方案

若要將函式作為參數傳遞，請使用 `[` 逸出整個字串，讓藍圖參數看起來像 `[[resourceGroup().tags.myTag]`。 逸出字元會導致「藍圖」在處理藍圖時，將值視為字串。 接著「藍圖」會將函式置於成品上，讓它如預期般地為動態函式。 有關詳細資訊，請參閱[Azure 資源管理器範本中的語法和運算式](../../../azure-resource-manager/templates/template-expressions.md)。

## <a name="delete-errors"></a>刪除錯誤

### <a name="scenario-assignment-deletion-timeout"></a><a name="assign-delete-timeout"></a>方案：分配刪除超時

#### <a name="issue"></a>問題

刪除藍圖分配未完成。

#### <a name="cause"></a>原因

刪除時，藍圖分配可能會陷入非終端狀態。 當由藍圖分配創建的資源仍然等待刪除或未將狀態碼返回到 Azure 藍圖時，將造成此狀態。

#### <a name="resolution"></a>解決方案

處於非終端狀態的藍圖分配在_6 小時_超時後自動標記為 **"失敗**"。 超時調整藍圖分配狀態後，可以重試刪除。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

- 通過[Azure 論壇](https://azure.microsoft.com/support/forums/)從 Azure 專家那裡獲得答案。
- 與[@AzureSupport](https://twitter.com/azuresupport)— 正式的 Microsoft Azure 帳戶連接，通過將 Azure 社區連接到正確的資源（答案、支援和專家），從而改善客戶體驗。
- 如果需要更多協助，您可以提出 Azure 支援事件。 轉到[Azure 支援網站](https://azure.microsoft.com/support/options/)並選擇 **"獲取支援**"。
---
title: 學習政策 - 個人化
description: 學習設置確定模型訓練的*超參數*。 在不同學習設置上訓練的兩個相同資料的模型最終將有所不同。
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: abe6a2a2ec9b9978230d894c69193469f6e932e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221211"
---
# <a name="learning-policy-and-settings"></a>學習策略和設置

學習設置確定模型訓練的*超參數*。 在不同學習設置上訓練的兩個相同資料的模型最終將有所不同。

[學習策略和設置](how-to-settings.md#configure-rewards-for-the-feedback-loop)設置在 Azure 門戶中的個人化程式資源上。

## <a name="import-and-export-learning-policies"></a>進出口學習政策

可以從 Azure 門戶導入和匯出學習策略檔。 使用此方法可以保存現有策略、測試它們、替換它們，並將其存檔到原始程式碼管理中，作為專案以供將來參考和審核。

[瞭解如何](how-to-manage-model.md#import-a-new-learning-policy)在 Azure 門戶中為個人化工具資源導入和匯出學習策略。

## <a name="understand-learning-policy-settings"></a>瞭解學習策略設置

學習策略中的設置不打算更改。 僅當您瞭解設置如何影響個人化程式時，才更改這些設置。 如果沒有這些知識，您可能會導致問題，包括使個人化模型無效。

個人化使用[誓言](https://github.com/VowpalWabbit)來訓練和評分事件。 請參閱關於如何使用母音搜索編輯學習設置的[母音](https://github.com/VowpalWabbit/vowpal_wabbit/wiki/Command-line-arguments)文檔。 獲得正確的命令列參數後，將命令保存到具有以下格式的檔（用所需命令替換參數屬性值），然後上載檔以導入個人化器資源的 Azure 門戶中的 **"模型和學習設置"** 窗格中的學習設置。

下面是`.json`學習策略的示例。

```json
{
  "name": "new learning settings",
  "arguments": " --cb_explore_adf --epsilon 0.2 --power_t 0 -l 0.001 --cb_type mtr -q ::"
}
```

## <a name="compare-learning-policies"></a>比較學習策略

您可以通過執行[離線評估](concepts-offline-evaluation.md)來比較不同的學習策略與個人化日誌中過去資料的性能。

[上傳您自己的學習策略](how-to-manage-model.md)，將其與當前的學習策略進行比較。

## <a name="optimize-learning-policies"></a>優化學習策略

個人化器可以在[離線評估](how-to-offline-evaluation.md)中創建優化的學習策略。 優化的學習策略在離線評估中具有更好的回報，在個人化中線上使用時，將產生更好的效果。

優化學習策略後，可以直接將其應用於個人化程式，以便立即替換當前策略。 或者，您可以保存優化的策略以進行進一步評估，然後決定是否放棄、保存或應用它。

## <a name="next-steps"></a>後續步驟

* 學習[活動和非活動事件](concept-active-inactive-events.md)。

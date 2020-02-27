---
title: 學習原則-個人化工具
description: 學習設定會決定模型定型的*超參數*。 在不同學習設定上定型之相同資料的兩種模型最終會有所不同。
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: abe6a2a2ec9b9978230d894c69193469f6e932e6
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623802"
---
# <a name="learning-policy-and-settings"></a>學習原則和設定

學習設定會決定模型定型的*超參數*。 在不同學習設定上定型之相同資料的兩種模型最終會有所不同。

您可以在 Azure 入口網站的個人化工具資源上設定[學習原則和設定](how-to-settings.md#configure-rewards-for-the-feedback-loop)。

## <a name="import-and-export-learning-policies"></a>匯入和匯出學習原則

您可以從 Azure 入口網站匯入和匯出學習原則檔案。 您可以使用這個方法來儲存現有的原則、加以測試、加以取代，然後在原始程式碼控制中將它們封存為成品，以供日後參考和審核。

瞭解[如何](how-to-manage-model.md#import-a-new-learning-policy)在個人化工具資源的 Azure 入口網站中匯入和匯出學習原則。

## <a name="understand-learning-policy-settings"></a>瞭解學習原則設定

學習原則中的設定不會進行變更。 只有在您瞭解它們如何影響個人化工具時，才變更設定。 若沒有此知識，您可能會造成問題，包括使個人化工具模型失效。

個人化工具使用[vowpalwabbit](https://github.com/VowpalWabbit)來定型和評分事件。 如需如何使用 vowpalwabbit 編輯學習設定的相關資訊，請參閱[vowpalwabbit 檔](https://github.com/VowpalWabbit/vowpal_wabbit/wiki/Command-line-arguments)。 當您有正確的命令列引數之後，請將命令儲存至具有下列格式的檔案（以所需的命令取代 arguments 屬性值），然後上傳檔案，以匯入個人化工具資源之 [**模型與學習設定**] Azure 入口網站窗格中的學習設定。

下列 `.json` 是學習原則的範例。

```json
{
  "name": "new learning settings",
  "arguments": " --cb_explore_adf --epsilon 0.2 --power_t 0 -l 0.001 --cb_type mtr -q ::"
}
```

## <a name="compare-learning-policies"></a>比較學習原則

您可以藉由執行[離線評估](concepts-offline-evaluation.md)，比較不同學習原則對個人化工具記錄中過去資料執行的方式。

[上傳您自己的學習原則](how-to-manage-model.md)，將其與目前的學習原則進行比較。

## <a name="optimize-learning-policies"></a>優化學習原則

個人化工具可以在[離線評估](how-to-offline-evaluation.md)中建立優化學習原則。 在離線評估中有更佳報酬的優化學習原則，在個人化工具中線上使用時，將會產生更佳的結果。

優化學習原則之後，您可以直接將它套用至個人化工具，讓它立即取代目前的原則。 或者，您可以儲存優化的原則以供進一步評估，並在稍後決定要捨棄、儲存或套用它。

## <a name="next-steps"></a>後續步驟

* 學習作用中[和非](concept-active-inactive-events.md)作用中的事件。

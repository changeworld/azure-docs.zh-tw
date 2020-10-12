---
title: 學習原則-個人化工具
description: 學習設定會決定模型定型的 *超參數* 。 在不同的學習設定上定型的兩個相同資料模型最後會有所不同。
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 1d2038e3796df843736eb80f7e5645f4141c82f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91253627"
---
# <a name="learning-policy-and-settings"></a>學習原則和設定

學習設定會決定模型定型的 *超參數* 。 在不同的學習設定上定型的兩個相同資料模型最後會有所不同。

您可以在 Azure 入口網站的個人化工具資源上設定[學習原則和設定](how-to-settings.md#configure-rewards-for-the-feedback-loop)。

## <a name="import-and-export-learning-policies"></a>匯入和匯出學習原則

您可以從 Azure 入口網站匯入和匯出學習原則檔。 您可以使用這個方法來儲存現有的原則、測試它們、取代它們，然後將它們保存在原始程式碼控制項中做為成品，以供未來參考和審核之用。

瞭解 [如何](how-to-manage-model.md#import-a-new-learning-policy) 在個人化工具資源的 Azure 入口網站中匯入和匯出學習原則。

## <a name="understand-learning-policy-settings"></a>瞭解學習原則設定

學習原則中的設定不適合變更。 只有當您瞭解它們對個人化工具有何影響時，才會變更設定。 如果沒有這種知識，您可能會造成問題，包括使個人化工具模型失效。

個人化工具會使用 [vowpalwabbit](https://github.com/VowpalWabbit) 來定型和評分事件。 請參閱有關如何使用 vowpalwabbit 編輯學習設定的 [vowpalwabbit 檔](https://github.com/VowpalWabbit/vowpal_wabbit/wiki/Command-line-arguments) 。 當您有正確的命令列引數之後，請將命令儲存至具有下列格式的檔案 (將 [引數] 屬性值取代為所需的命令) 然後上傳檔案，以匯入 [模型和學習設定] 窗格中的 [ **模型與學習設定** ] 窗格 Azure 入口網站中個人化工具資源的 [學習設定]。

以下 `.json` 是學習原則的範例。

```json
{
  "name": "new learning settings",
  "arguments": " --cb_explore_adf --epsilon 0.2 --power_t 0 -l 0.001 --cb_type mtr -q ::"
}
```

## <a name="compare-learning-policies"></a>比較學習原則

您可以執行 [離線評估](concepts-offline-evaluation.md)，以比較不同的學習原則如何針對個人化工具記錄中的過去資料執行。

[上傳您自己的學習原則](how-to-manage-model.md) ，以與目前的學習原則進行比較。

## <a name="optimize-learning-policies"></a>優化學習原則

個人化工具可以在 [離線評估](how-to-offline-evaluation.md)中建立優化的學習原則。 在離線評估中具有更佳報酬率的優化學習原則，會在個人化工具線上使用時產生更好的結果。

優化學習原則之後，您可以直接將它套用至個人化工具，讓它立即取代目前的原則。 或者，您可以儲存優化的原則以進行進一步評估，並于稍後決定要捨棄、儲存或套用該原則。

## <a name="next-steps"></a>後續步驟

* 瞭解作用中 [和非](concept-active-inactive-events.md)使用中的事件。

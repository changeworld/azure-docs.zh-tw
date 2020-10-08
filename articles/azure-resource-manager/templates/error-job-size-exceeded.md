---
title: 作業大小超過錯誤
description: 描述當工作大小或範本太大時，如何針對錯誤進行疑難排解。
ms.topic: troubleshooting
ms.date: 10/07/2020
ms.openlocfilehash: 638bdef246fc908ab997bfb99e7526febdb3792e
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91822146"
---
# <a name="resolve-errors-for-job-size-exceeded"></a>解決超過作業大小的錯誤

本文說明如何解決 **JobSizeExceededException** 和 **DeploymentSizeExceededException** 錯誤。

## <a name="symptom"></a>徵狀

部署範本時，您會收到錯誤，指出部署已超過限制。

## <a name="cause"></a>原因

當範本的大小超過 4 MB 時，您可能會收到此錯誤。 針對使用 [複製](copy-resources.md) 來建立多個實例的資源定義，在擴充後，4 MB 的限制會套用至範本的最終狀態。 最終狀態也包含變數和參數的解析值。

部署作業也包含要求的相關中繼資料。 針對大型範本，與範本結合的中繼資料可能會超過作業允許的大小。

範本的其他限制如下：

* 256 個參數
* 256 個變數
* 800 個資源 (包括複本計數)
* 64 個輸出值
* 範本運算式中的 24,576 個字元

## <a name="solution-1---simplify-template"></a>解決方案 1-簡化範本

第一個選項是簡化範本。 當您的範本部署許多不同的資源類型時，此選項可運作。 請考慮將範本分割成 [連結的範本](linked-templates.md)。 將您的資源類型分割為邏輯群組，並為每個群組新增連結的範本。 例如，如果您需要部署許多網路資源，您可以將這些資源移至連結的範本。

您可以將其他資源設定為相依于連結的範本，並 [從連結的範本的輸出中取得值](linked-templates.md#get-values-from-linked-template)。

## <a name="solution-2---reduce-name-size"></a>解決方案 2-減少名稱大小

請嘗試縮短您用於 [參數](template-parameters.md)、 [變數](template-variables.md)和 [輸出](template-outputs.md)的名稱長度。 當這些值透過複製迴圈重複時，會將大型名稱乘以多次。

## <a name="solution-3---use-serial-copy"></a>解決方案 3-使用序列複製

第二個選項是將複製迴圈從 [平行變更為序列處理](copy-resources.md#serial-or-parallel)。 只有當您懷疑錯誤是透過複製來部署大量資源時，才使用此選項。 這項變更可能會大幅增加您的部署時間，因為不會以平行方式部署資源。

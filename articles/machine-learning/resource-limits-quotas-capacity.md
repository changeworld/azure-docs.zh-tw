---
title: 服務限制
titleSuffix: Azure Machine Learning
description: 用於容量規劃的服務限制，以及 Azure Machine Learning 的要求和回應上限。
services: machine-learning
author: andscho
ms.author: andscho
ms.reviewer: mldocs
ms.topic: reference
ms.service: machine-learning
ms.subservice: core
ms.date: 12/21/2020
ms.openlocfilehash: 132c4b223b99f7110cd4553bb0dd93f1e58f4cdd
ms.sourcegitcommit: a89a517622a3886b3a44ed42839d41a301c786e0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2020
ms.locfileid: "97733292"
---
# <a name="service-limits-in-azure-machine-learning"></a>Azure Machine Learning 中的服務限制

本節列出 Azure Machine Learning 中的基本配額和節流閾值。

## <a name="workspaces"></a>工作區
| 限制 | 值 |
| --- | --- |
| 工作區名稱 | 2-32 個字元 |

## <a name="runs"></a>執行
| 限制 | 值 |
| --- | --- |
| 每個工作區執行 | 1000 萬 |
| RunId/ParentRunId | 256 個字元 |
| DataContainerId | 261個字元 |
| DisplayName |256 個字元|
| 描述 |5000個字元|
| 屬性數目 |50 |
| 屬性索引鍵的長度 |100 個字元 |
| 屬性值的長度 |1000個字元 |
| 標記數目 |50 |
| 標記索引鍵的長度 |100 |
| 標記值的長度 |1000個字元 |
| CancelUri / CompleteUri / DiagnosticsUri |1000個字元 |
| 錯誤訊息長度 |3000個字元 |
| 警告訊息長度 |300個字元 |
| 輸入資料集的數目 |200 |
| 輸出資料集的數目 |20 |


## <a name="metrics"></a>計量
| 限制 | 值 |
| --- | --- |
| 每次執行的度量名稱 |50|
| 度量資料列（每個計量名稱） |1000 萬|
| 每個度量資料列的資料行 |15|
| 度量資料行名稱長度 |255 個字元 |
| 度量資料行值長度 |255 個字元 |
| 每批次上傳的度量資料列 | 250 |

> [!NOTE]
> 如果您要在每次執行時達到計量名稱的限制，因為您要將變數格式化為計量名稱，請考慮改為使用其中一個資料行是變數值的資料列度量，而第二個數據行是度量值。

## <a name="artifacts"></a>Artifacts

| 限制 | 值 |
| --- | --- |
| 每次執行的構件數目 |1000 萬|
| 成品路徑的最大長度 |5000個字元 |

## <a name="limit-increases"></a>限制增加
您可以藉由 [聯絡支援](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)人員來增加個別工作區的某些限制。 

## <a name="next-steps"></a>後續步驟

- [設定您的 Azure Machine Learning 環境](how-to-configure-environment.md)

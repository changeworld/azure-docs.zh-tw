---
title: 包含檔案
description: 包含檔案
services: batch
author: LauraBrenner
ms.service: batch
ms.topic: include
ms.date: 05/28/2019
ms.author: labrenne
ms.custom: include file
ms.openlocfilehash: a0d74aa38dffdd41fbe617066391ef271a507349
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81737012"
---
| **Resource** | **預設限制** | **上限** |
| --- | --- | --- |
| 每個訂閱每個區域的 Azure 批次處理帳戶 | 1-3 |50 |
| 每一 Batch 帳戶的專用核心 | 90-900 | 請連絡支援人員 |
| 每一 Batch 帳戶的低優先順序核心 | 10-100 | 請連絡支援人員 |
| 每個批次處理帳號**[的活動](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** 工作與工作計劃(**已完成**的作業沒有限制 ) | 100-300 | 1,000<sup>1</sup> |
| 每一 Batch 帳戶的集區 | 20-100 | 500<sup>1</sup> |

<sup>1</sup>要請求超出此限制的增幅,請與 Azure 支援部門聯繫。

> [!NOTE]
> 預設限制會根據您用來建立 Batch 帳戶的訂用帳戶類型而有所不同。 所顯示的核心配額是針對 Batch 服務模式中的 Batch 帳戶。 [檢視您 Batch 帳戶中的配額](../articles/batch/batch-quota-limit.md#view-batch-quotas)。

> [!IMPORTANT]
> 為了幫助我們在全球衛生大流行期間更好地管理容量,某些區域和某些類型的訂閱的新 Batch 帳戶的預設核心配額已從上述值範圍(在某些情況下為零核心)減少到零。 建立新 Batch 帳戶時,[請檢查核心配額](../articles/batch/batch-quota-limit.md#view-batch-quotas)並[請求增加核心配額](../articles/batch/batch-quota-limit.md#increase-a-quota)(如果需要)。 

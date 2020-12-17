---
title: 包含檔案
description: 包含檔案
services: batch
author: JnHs
ms.service: batch
ms.topic: include
ms.date: 12/16/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 0a94b122f1cdd598eeac553c8cc784d2a0a5369f
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97614503"
---
| **Resource** | **預設限制** | **上限** |
| --- | --- | --- |
| Azure Batch 帳戶 (每一區域的每一訂用帳戶) | 1-3 |50 |
| 每一 Batch 帳戶的專用核心 | 90-900 | 請連絡支援人員 |
| 每一 Batch 帳戶的低優先順序核心 | 10-100 | 請連絡支援人員 |
| **[作用中的](/rest/api/batchservice/job/get#jobstate)** 工作和每一 Batch 帳戶的作業排程 (**已完成** 作業沒有限制) | 100-300 | 1,000<sup>1</sup> |
| 每一 Batch 帳戶的集區 | 20-100 | 500<sup>1</sup> |

<sup>1</sup> 若想要要求增加到超過此限制，請連絡 Azure 支援。

> [!NOTE]
> 預設限制會根據您用來建立 Batch 帳戶的訂用帳戶類型而有所不同。 所顯示的核心配額是針對 Batch 服務模式中的 Batch 帳戶。 [檢視您 Batch 帳戶中的配額](../articles/batch/batch-quota-limit.md#view-batch-quotas)。

> [!IMPORTANT]
> 為了協助我們更有效地管理全球流感期間的產能，某些區域中新 Batch 帳戶的預設核心配額，以及某些類型的訂用帳戶，在某些情況下會從上述值範圍中縮減為零個核心。 當您建立新的 Batch 帳戶時，[檢查您的核心配額](../articles/batch/batch-quota-limit.md#view-batch-quotas) 並視需要[要求增加的核心配額](../articles/batch/batch-quota-limit.md#increase-a-quota)。 或者，考慮重新使用已具有足夠配額的 Batch 帳戶。
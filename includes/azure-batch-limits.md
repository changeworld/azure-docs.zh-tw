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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81737012"
---
| **Resource** | **預設限制** | **上限** |
| --- | --- | --- |
| 每個訂用帳戶每個區域 Azure Batch 帳戶 | 1-3 |50 |
| 每一 Batch 帳戶的專用核心 | 90-900 | 請連絡支援人員 |
| 每一 Batch 帳戶的低優先順序核心 | 10-100 | 請連絡支援人員 |
| 每一 Batch 帳戶的作用中作業和作業排程（**已完成****[的工作沒有](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** 限制） | 100-300 | 1000<sup>1</sup> |
| 每一 Batch 帳戶的集區 | 20-100 | 500<sup>1</sup> |

<sup>1</sup>若要要求增加超過此限制，請聯絡 Azure 支援。

> [!NOTE]
> 預設限制會根據您用來建立 Batch 帳戶的訂用帳戶類型而有所不同。 所顯示的核心配額是針對 Batch 服務模式中的 Batch 帳戶。 [檢視您 Batch 帳戶中的配額](../articles/batch/batch-quota-limit.md#view-batch-quotas)。

> [!IMPORTANT]
> 為了協助我們更有效地管理全域健全狀況流感期間的容量，某些區域中新 Batch 帳戶的預設核心配額，以及某些類型的訂用帳戶，在某些情況下會從上述的值範圍縮減為零個核心。 當您建立新的 Batch 帳戶時，請[檢查您的核心配額](../articles/batch/batch-quota-limit.md#view-batch-quotas)，並[要求增加核心配額](../articles/batch/batch-quota-limit.md#increase-a-quota)（如有需要）。 

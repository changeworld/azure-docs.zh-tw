---
title: Azure Databricks 預先購買折扣的套用方式
description: 了解 Azure Databricks 預先購買折扣如何套用至您的使用量。 您可以在購買期限內隨時使用這些 Databricks。
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: f5a2102cedfa2b8631c1eb7ff6a3ff2452d315e3
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690522"
---
# <a name="how-azure-databricks-pre-purchase-discount-is-applied"></a>Azure Databricks 預先購買折扣的套用方式

在購買期限內，您隨時都可以使用預先購買的 Azure Databricks 認可單位 (DBCU)。 任何 Azure Databricks 使用量都會自動從預先購買的 DBCU 扣除。

不同於 VM，預先購買的單位不會每小時到期。 在購買期限內，您隨時都可以使用預先購買的單位。 若要取得預先購買折扣，您不需要將預先購買的方案重新部署或指派到 Azure Databricks 工作區來獲得使用量。

預先購買折扣只會套用至 Azure Databricks 單位 (DBU) 使用量。 其他費用 (例如計算、儲存體和網路) 會個別收費。

## <a name="pre-purchase-discount-application"></a>預先購買折扣的套用

Databricks 預先購買會套用至所有 Databricks 工作負載和層級。 您可以將預先購買視為預先付費的 Databricks 認可單位集區。 不論是工作負載還是層級，使用量都會從集區中扣除。 使用量會以下列比率扣除：

| **[工作負載]** | **DBU 套用比率 - 標準層** | **DBU 套用比率 - 進階層** |
| --- | --- | --- |
| 資料分析 | 0.4 | 0.55 |
| 資料工程 | 0.15 | 0.30 |
| 輕量資料工程 | 0.07 | 0.22 |

例如，取用「資料分析 - 標準層」的數量時，預先購買的 Databricks 認可單位會扣除 0.4 單位。 使用「輕量資料工程 - 標準層」的數量時，預先購買的 Databricks 認可單位會扣除 0.07 單位

## <a name="determine-plan-use"></a>確定方案的使用情形

若要確定 DBCU 方案的使用情形，請移至 Azure 入口網站 > [保留]，然後按一下已購買的 Databricks 方案。 隨即會顯示您至今為止的使用情形和任何剩餘的單位。 如需如何確定保留使用情形的詳細資訊，請參閱[查看保留使用量](reservation-apis.md#see-reservation-usage)一文。

## <a name="how-discount-application-shows-in-usage-data"></a>折扣套用方式會顯示在使用量資料中

當預先購買折扣套用至 Databricks 使用量時，使用量資料中所顯示的隨選費用會是零。 如需保留成本和使用量資料的詳細資訊，請參閱[取得 Enterprise 合約保留成本和使用量](understand-reserved-instance-usage-ea.md)。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>後續步驟

- 若要了解如何管理保留項目，請參閱[管理 Azure 保留項目](manage-reserved-vm-instance.md)。
- 若要深入了解如何預先購買 Azure Databricks 以節省成本，請參閱[使用預先購買來最佳化 Azure Databricks 成本](prepay-databricks-reserved-capacity.md)。
- 若要深入了解 Azure 保留項目，請參閱下列文章：
  - [什麼是 Azure 保留項目？](save-compute-costs-reservations.md)
  - [管理 Azure 中的保留](manage-reserved-vm-instance.md)
  - [了解採用隨用隨付費率的訂用帳戶的保留使用量](understand-reserved-instance-usage.md)
  - [了解 Enterprise 註冊的保留項目使用量](understand-reserved-instance-usage-ea.md)

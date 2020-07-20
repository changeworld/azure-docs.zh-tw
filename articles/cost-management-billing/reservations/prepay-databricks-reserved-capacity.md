---
title: 使用預先購買來最佳化 Azure Databricks 成本
description: 了解如何使用保留容量預付 Azure Databricks 費用來節省成本。
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: b000934bb218c21a454ed04afe4e9a4495e808ab
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "77200379"
---
# <a name="optimize-azure-databricks-costs-with-a-pre-purchase"></a>使用預先購買來最佳化 Azure Databricks 成本

當您預先購買一或三年的 Azure Databricks 認可單位 (DBCU) 時，可以節省 Azure Databricks 單位 (DBU) 成本。 在購買期限內，您隨時都可以使用預先購買的 DBCU。 不同於 VM，預先購買的單位不會每小時到期，您可以在購買期限內隨時使用。

任何 Azure Databricks 都會自動從預先購買的 DBU 使用扣除。 您不需要將預先購買的方案重新部署或指派到 Azure Databricks 工作區來獲得 DBU 使用量，即可取得預先購買折扣。

預先購買折扣只會套用至 DBU 使用量。 其他費用 (例如計算、儲存體和網路) 會個別收費。

## <a name="determine-the-right-size-to-buy"></a>決定要購買的正確大小

Databricks 預先購買會套用至所有 Databricks 工作負載和層級。 您可以將預先購買視為預先付費的 Databricks 認可單位集區。 不論是工作負載還是層級，使用量都會從集區中扣除。 使用量會以下列比率扣除：

| **[工作負載]** | **DBU 套用比率 - 標準層** | **DBU 套用比率 - 進階層** |
| --- | --- | --- |
| 資料分析 | 0.4 | 0.55 |
| 資料工程 | 0.15 | 0.30 |
| 輕量資料工程 | 0.07 | 0.22 |

例如，取用「資料分析 - 標準層」的數量時，預先購買的 Databricks 認可單位會扣除 0.4 單位。

購買之前，請先計算不同工作負載和層級所取用的 DBU 總數。 使用上述比率來正規化為 DBCU，然後預測接下來一或三年的總使用量。

## <a name="purchase-databricks-commit-units"></a>購買 Databricks 認可單位

您可以在 [Azure 入口網站](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D)中購買 Databricks 方案。 若要購買保留容量，您必須擁有至少一個企業訂用帳戶的擁有者角色。

- 您必須擔任下列項目的擁有者角色：至少一個 Enterprise 合約 (供應項目號碼：MS-AZR-0017P 或 MS-AZR-0148P) 或 Microsoft 客戶合約，或是使用隨用隨付費率的個別訂用帳戶 (供應項目號碼：MS-AZR-0003P 或 MS-AZR-0023P)。
- 針對 EA 訂用帳戶，必須在 EA 入口網站中啟用 [新增保留執行個體] 選項。 或者，如果該設定已停用，則您必須是訂用帳戶的 EA 管理員。
- 針對企業訂用帳戶，必須在 [EA 入口網站](https://ea.azure.com/)中啟用**新增保留執行個體**。 或者，如果該設定已停用，則您必須是訂用帳戶的 EA 系統管理員。

**若要購買：**

1. 移至 [Azure 入口網站](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D)。
1. 選取一個訂用帳戶。 使用 [訂用帳戶]  清單來選取用來支付保留容量的訂用帳戶。 訂用帳戶的付款方式為收取保留容量的預付費用。 費用會從註冊的承諾用量金額餘額扣除或作為超額部分收費。
1. 選取範圍。 使用 [範圍]  清單來選取訂用帳戶範圍：
    - **單一資源群組範圍** — 只會將保留折扣套用至所選資源群組中的相符資源。
    - **單一訂用帳戶範圍** — 會將保留折扣套用至所選訂用帳戶中的相符資源。
    - **共用範圍** — 會將保留折扣套用至計費內容中合格訂用帳戶的相符資源。 針對 Enterprise 合約客戶，計費內容為註冊。
1. 選取您想要購買多少 Azure Databricks 認可單位，並完成購買。


![顯示 Azure 入口網站中 Azure Databricks 購買的範例](./media/prepay-databricks-reserved-capacity/data-bricks-pre-purchase.png)

## <a name="change-scope-and-ownership"></a>變更範圍和擁有權

購買之後，您可以對保留進行下列類型的變更：

- 更新保留範圍
- 角色型存取

您無法分割或合併 Databricks 認可單位預先購買。 如需如何管理保留的詳細資訊，請參閱[購買後管理保留](manage-reserved-vm-instance.md)。

## <a name="cancellations-and-exchanges"></a>取消和交換

Databricks 預先購買方案不支援取消和交換。 所有購買都是無法改變的。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>後續步驟

- 若要深入了解 Azure 保留項目，請參閱下列文章：
  - [什麼是 Azure 保留項目？](save-compute-costs-reservations.md)
  - [了解 Azure Databricks 預先購買 DBCU 折扣的套用方式](reservation-discount-databricks.md)
  - [了解 Enterprise 註冊的保留項目使用量](understand-reserved-instance-usage-ea.md)

---
title: 適用於 Azure 保留自動化的 API | Microsoft Docs
description: 了解您可以用來以程式設計方式取得保留資訊的 Azure API。
author: yashesvi
ms.reviewer: yashesvi
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: c2e590f011c92df6aa942e8e1fc799023f15750f
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200345"
---
# <a name="apis-for-azure-reservation-automation"></a>適用於 Azure 保留自動化的 API

使用 Azure API 以程式設計方式為您的組織取得 Azure 服務或軟體保留的資訊。

## <a name="find-reservation-plans-to-buy"></a>尋找要購買的保留方案

如需根據您組織的使用量來購買保留方案，請使用「保留建議 API」來取得建議。 如需詳細資訊，請參閱[取得保留建議](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)。

您也可以使用「耗用量 API 使用量詳細資料」來分析您的資源使用量。 如需詳細資訊，請參閱[使用量詳細資料 - 依帳單帳戶列出的計費週期清單](/rest/api/consumption/usagedetails/list#billingaccountusagedetailslistforbillingperiod-legacy)。 您持續使用的 Azure 資源，通常是保留的最佳候選項目。

## <a name="buy-a-reservation"></a>購買保留項目

您可以使用 REST API，以程式設計方式購買 Azure 保留及軟體方案。 若要深入了解，請參閱[保留訂單 - 購買 API](/rest/api/reserved-vm-instances/reservationorder/purchase)。

以下是使用 REST API 進行購買的範例要求：

```
PUT https://management.azure.com/providers/Microsoft.Capacity/reservationOrders/<GUID>?api-version=2019-04-01
```

要求本文：

```
{
 "sku": {
    "name": "standard_D1"
  },
 "location": "westus",
 "properties": {
    "reservedResourceType": "VirtualMachines",
    "billingScopeId": "/subscriptions/ed3a1871-612d-abcd-a849-c2542a68be83",
    "term": "P1Y",
    "quantity": "1",
    "displayName": "TestReservationOrder",
    "appliedScopes": null,
    "appliedScopeType": "Shared",
    "reservedResourceProperties": {
      "instanceFlexibility": "On"
    }
  }
}
```

您也可以在 Azure 入口網站中購買保留。 如需詳細資訊，請參閱下列文章：

服務方案：
- [虛擬機器](../../virtual-machines/windows/prepay-reserved-vm-instances.md?toc=/azure/billing/TOC.json)
-  [Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md?toc=/azure/billing/TOC.json)
- [SQL Database](../../sql-database/sql-database-reserved-capacity.md?toc=/azure/billing/TOC.json)

軟體方案：
- [SUSE Linux 軟體](../../virtual-machines/linux/prepay-suse-software-charges.md?toc=/azure/billing/TOC.json)

## <a name="get-reservations"></a>取得保留

如果您是有 Enterprise 合約 (EA 客戶) 的 Azure 客戶，您可以使用[取得保留執行個體收費 API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) 來取得您組織購買的保留。 對於其他訂用帳戶，請使用 API [保留訂單 - 清單](/rest/api/reserved-vm-instances/reservationorder/list)來取得您所購買且有權限檢視之保留的清單。 根據預設，帳戶擁有者或購買保留的人員，有權限檢視該保留。

## <a name="see-reservation-usage"></a>查看保留使用量

如果您是 EA 客戶，您可以程式設計方式檢視您組織中之保留的使用方式。 如需詳細資訊，請參閱[取得企業客戶的保留執行個體使用量](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)。 對於其他訂用帳戶，可以使用 API[保留項目摘要 - 依保留順序和保留列出的清單](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation)。

如果您發現您組織的保留太少被使用：

- 請確認您組織建立的虛擬機器符合保留上的 VM 大小。
- 請確認執行個體大小彈性已開啟。 如需詳細資訊，請參閱[管理保留 - 變更保留的 VM 執行個體的最佳化設定](manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances)。
- 變更要共用的保留範圍，使它更廣泛地套用。 如需詳細資訊，請參閱[管理保留 - 變更保留範圍](manage-reserved-vm-instance.md#change-the-reservation-scope)。
- 交換未使用的數量。 如需詳細資訊，請參閱[管理保留](manage-reserved-vm-instance.md)。

## <a name="give-access-to-reservations"></a>提供保留的存取權

使用[保留 - 作業 - 清單 API](/rest/api/reserved-vm-instances/reservationorder/list) 來取得使用者有權存取的所有保留的清單。 若要以程式設計方式提供保留的存取權，請參閱下列其中一篇文章：

- [使用 RBAC 和 REST API 來管理存取權](../../role-based-access-control/role-assignments-rest.md)
- [使用 RBAC 和 Azure PowerShell 來管理存取權](../../role-based-access-control/role-assignments-powershell.md)
- [使用 RBAC 和 Azure CLI 來管理存取權](../../role-based-access-control/role-assignments-cli.md)

## <a name="split-or-merge-reservation"></a>分割或合併保留項目

購買保留內的多個資源執行個體之後，建議您將該保留中的執行個體指派至不同的訂用帳戶。 您可以變更保留範圍，使它套用至相同計費內容內的所有訂用帳戶。 但是，基於成本管理或預算考量，您可能想要保持範圍是「單一訂用帳戶」，並將保留執行個體指派至特定訂用帳戶。

若要分割保留，請使用 API [保留 - 分割](/rest/api/reserved-vm-instances/reservation/split)。 您也可以使用 PowerShell 來分割保留。 如需詳細資訊，請參閱[管理保留 - 將保留分割成兩個保留](manage-reserved-vm-instance.md#split-a-single-reservation-into-two-reservations)。

若要將兩個保留合併成一個保留，請使用 API [保留 - 合併](/rest/api/reserved-vm-instances/reservation/merge)。

## <a name="change-scope-for-a-reservation"></a>變更保留範圍

保留的範圍可以是單一訂用帳戶、單一資源群組，或您的計費內容中的所有訂用帳戶。 如果您將範圍設定為單一訂用帳戶或單一資源群組，此保留會對應至選取的訂用帳戶中所執行的資源。 如果您刪除或移動訂用帳戶或資源群組，則不會使用保留。  如果您將範圍設定為共用，Azure 會將保留對應至計費內容中所有訂用帳戶上執行的資源。 計費內容取決於您購買此保留所用的訂用帳戶。 您可以在購買時選取範圍，或在購買後隨時加以變更。 如需詳細資訊，請參閱[管理保留 - 變更範圍](manage-reserved-vm-instance.md#change-the-reservation-scope)。

若要以程式設計方式變更範圍，請使用 API [保留 - 更新](/rest/api/reserved-vm-instances/reservation/update)。

## <a name="learn-more"></a>深入了解

- [什麼是 Azure 的保留](save-compute-costs-reservations.md)
- [了解 VM 保留折扣的套用方式](../manage/understand-vm-reservation-charges.md)
- [了解如何套用 SUSE Linux Enterprise 軟體方案折扣](understand-suse-reservation-charges.md)
- [了解其他保留折扣的套用方式](understand-reservation-charges.md)
- [了解隨用隨付訂用帳戶的保留使用量](understand-reserved-instance-usage.md)
- [了解 Enterprise 註冊的保留項目使用量](understand-reserved-instance-usage-ea.md)
- [Windows 軟體的成本不包括在保留項目內](reserved-instance-windows-software-costs.md)
- [合作夥伴中心雲端解決方案提供者 (CSP) 計畫中的 Azure 保留項目](https://docs.microsoft.com/partner-center/azure-reservations)

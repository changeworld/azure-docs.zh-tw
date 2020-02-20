---
title: 檢視 Azure 資源的保留 | Microsoft Docs
description: 了解如何在 Azure 入口網站中檢視 Azure 保留。
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 5c9d9074e4b8d0d9e36417daee4d58c1d9b28b64
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199240"
---
# <a name="view-azure-reservations-in-the-azure-portal"></a>在 Azure 入口網站中檢視 Azure 保留

您可以透過數種方式檢視 Azure 的保留，視您的訂用帳戶類型和權限而定。

## <a name="view-purchased-reservations"></a>檢視已購買的保留

當您購買保留時，您和帳戶管理員預設可以檢視該保留。 您和帳戶管理員會自動取得保留訂單和保留的「擁有者」角色。 若要允許其他人員檢視保留，您必須將他們新增為保留訂單或保留的「擁有者」  或「讀者」  。

如需詳細資訊，請參閱[新增或變更可以管理保留的使用者](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation)。

若要以「擁有者」或「讀者」身分檢視保留，

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 搜尋 [保留]  。
    ![顯示 Azure 入口網站搜尋的螢幕擷取畫面](./media/view-reservations/portal-reservation-search.png)  
3. 清單中會顯示您具備「擁有者」或「讀者」角色的所有保留。 每個保留都會顯示最後的已知使用率百分比。  
    ![顯示保留清單的範例](./media/view-reservations/view-reservations.png)
4. 選取保留，並查看過去五天的使用率趨勢。  
    ![顯示保留使用率趨勢的範例](./media/view-reservations/reservation-utilization.png)
5. 您也可以使用「保留執行個體使用量 API」和 [Microsoft Azure 使用量見解 Power BI 內容套件](/power-bi/service-connect-to-azure-consumption-insights)，來取得[保留使用率](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)。

如果您需要變更保留的範圍、分割保留或變更可管理保留的人員，請參閱 [管理 Azure Reservations](manage-reserved-vm-instance.md)。

## <a name="view-reservation-transactions-for-enterprise-enrollments"></a>檢視 Enterprise 註冊的保留交易

 如果您有合作夥伴主導的 Enterprise 註冊，請移至 EA 入口網站中的 [報告]  來檢視保留。 針對其他 Enterprise 註冊，您可以在 EA 入口網站及 Azure 入口網站中檢視保留。 您必須是 EA 系統管理員，才能檢視保留交易。

若要在 Azure 入口網站中檢視保留交易，

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋 [成本管理 + 帳單]  。

    ![顯示 Azure 入口網站搜尋的螢幕擷取畫面](./media/view-reservations/portal-cm-billing-search.png)

1. 選取 [保留交易]  。
1. 若要篩選結果，請選取 [時間範圍]  、[類型]  或 [描述]  。
1. 選取 [套用]  。

    ![顯示「保留交易」結果的螢幕擷取畫面](./media/view-reservations/portal-billing-reservation-transaction-results.png)

若要使用 API 來取得資料，請參閱[取得企業客戶的保留執行個體交易費用](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges)。

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure 保留項目，請參閱下列文章：

- [什麼是 Azure 的保留？](save-compute-costs-reservations.md)
- [管理 Azure 的保留](manage-reserved-vm-instance.md)

購買服務方案：

- [預付 Cosmos DB 保留容量的費用](../../cosmos-db/cosmos-db-reserved-capacity.md)
- [以 Azure SQL Database 保留容量預先支付 SQL 資料庫計算資源的費用](../../sql-database/sql-database-reserved-capacity.md)
- [預付具有 Azure 保留 VM 執行個體的虛擬機器](../../virtual-machines/windows/prepay-reserved-vm-instances.md)

購買軟體方案：

- [從 Azure 保留預付 Red Hat 軟體方案](../../virtual-machines/linux/prepay-rhel-software-charges.md)
- [從 Azure 保留預付 SUSE 軟體方案](../../virtual-machines/linux/prepay-suse-software-charges.md)

了解使用量：

- [了解隨用隨付訂用帳戶的保留使用量](understand-reserved-instance-usage.md)
- [了解 Enterprise 註冊的保留項目使用量](understand-reserved-instance-usage-ea.md)
- [了解 CSP 訂用帳戶的保留使用量](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡

如有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

---
title: 檢視 Azure 資源的保留 | Microsoft Docs
description: 了解如何在 Azure 入口網站中檢視 Azure 保留。 使用 API、PowerShell、CLI 和 Power BI 查看保留和使用率。
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 926e0c8bc0fdff580b4d1e84be5940e0945d52ae
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92146272"
---
# <a name="view-azure-reservations"></a>檢視 Azure 保留

您可以從 Azure 入口網站檢視及管理已購買的保留。

## <a name="permissions-to-view-a-reservation"></a>檢視保留的許可權

若要檢視或管理保留，您必須擁有其「讀者」或「擁有者」許可權。 根據預設，當您購買保留時，您和帳戶管理員會自動取得保留訂單和保留的「擁有者」角色。 若要允許其他人員檢視保留，您必須將他們新增為保留訂單或保留的「擁有者」  或「讀者」  。 將某人新增至提供進行保留計費的訂用帳戶，並不會自動將其新增至保留。 

如需詳細資訊，請參閱[新增或變更可以管理保留的使用者](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation)。

## <a name="view-reservation-and-utilization-in-the-azure-portal"></a>檢視 Azure 入口網站中的保留和使用率

若要以「擁有者」或「讀者」身分檢視保留

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 移至[保留](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)。
3. 清單將會顯示您具備「擁有者」或「讀者」角色的所有保留。 每個保留都會顯示最後的已知使用率百分比。
4. 按一下 [使用率百分比]，以查看使用量歷程記錄和詳細資料。 請參閱下列影片中的詳細資料。
   > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4sYwk] 

## <a name="get-reservations-and-utilization-using-apis-powershell-cli"></a>使用 API、PowerShell、CLI 取得保留和使用率

使用下列資源取得所有保留的清單
- [API：保留訂單 - 清單](/rest/api/reserved-vm-instances/reservationorder/list)
- [PowerShell：保留訂單 - 清單](/powershell/module/azurerm.reservations/get-azurermreservationorder)
- [CLI：保留訂單 - 清單](/cli/azure/reservations/reservation-order#az-reservations-reservation-order-list)

您也可以使用保留執行個體使用方式 API 來取得[保留使用率](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)。 

## <a name="see-reservations-and-utilization-in-power-bi"></a>請參閱 Power BI 中的保留和使用率

Power BI 的使用者有兩個選項
- 內容套件：[耗用量深入解析 Power BI 內容套件](/power-bi/desktop-connect-azure-cost-management)中，提供保留購買和使用率資料。 使用此內容套件來建立您想要的報告。 
- 成本管理應用程式：針對預先建立的報告，使用[成本管理應用程式](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp)，可供您進一步進行自訂。

## <a name="next-steps"></a>後續步驟

- [管理 Azure 保留](manage-reserved-vm-instance.md)。
- [了解隨用隨付訂用帳戶的保留使用量](understand-reserved-instance-usage.md)
- [了解 Enterprise 註冊的保留項目使用量](understand-reserved-instance-usage-ea.md)
- [了解 CSP 訂用帳戶的保留使用量](/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡

如有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。
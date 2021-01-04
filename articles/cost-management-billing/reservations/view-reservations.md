---
title: 檢視 Azure 資源的保留
description: 了解如何在 Azure 入口網站中檢視 Azure 保留。 使用 API、PowerShell、CLI 和 Power BI 查看保留和使用率。
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 12/15/2020
ms.author: banders
ms.openlocfilehash: 8c69f477f363654b8bd707949f0a5b4c46a4e8df
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97561099"
---
# <a name="view-azure-reservations"></a>檢視 Azure 保留

本文將說明如何在 Azure 入口網站中檢視 Azure 保留。 您可以在 Azure 入口網站中檢視及管理已購買的保留。

## <a name="who-can-manage-a-reservation-by-default"></a>預設可以管理保留的人員

根據預設，下列使用者可以檢視和管理保留：

- 購買保留的人員，以及購買保留所用計費訂用帳戶的帳戶管理員，都會新增至保留訂單。
- Enterprise 合約和 Microsoft 客戶合約的計費管理員。

若要允許其他人管理保留，您有兩個選項：

- 委派個別保留訂單的存取管理：
    1. 登入 [Azure 入口網站](https://portal.azure.com)。
    1. 選取 [所有服務] >  [保留] 來列出您具有存取權的保留。
    1. 選取您想要將存取權委派給其他使用者的保留。
    1. 從 [保留詳細資料] 中選取保留訂單。
    1. 選取 [存取控制 (IAM)]。
    1. 選取 [新增角色指派] > [角色] > [擁有者]。 如果您想要給予有限的存取權，可選取不同角色。
    1. 輸入要新增為擁有者之使用者的電子郵件地址。
    1. 選取使用者，然後選取 [儲存]。

- 將使用者新增為 Enterprise 合約或 Microsoft 客戶合約的計費管理員：
    - 針對 Enterprise 合約，需以「企業系統管理員」角色新增使用者，才能檢視及管理套用至 Enterprise 合約的所有保留訂單。 具有「企業系統管理員 (唯讀)」角色的使用者只能檢視保留。 部門系統管理員和帳戶擁有者無法檢視保留，_除非_ 使用存取控制 (IAM) 明確地將他們新增至保留中。 如需詳細資訊，請參閱[管理 Azure 企業角色](../manage/understand-ea-roles.md)。

        企業系統管理員可以取得保留訂單的擁有權，也可以使用存取控制 (IAM) 將其他使用者新增至保留。
    - 若為 Microsoft 客戶合約，使用者如果具有「帳單設定檔擁有者」角色或「帳單設定檔參與者」角色，即可管理使用帳單設定檔所購買的所有保留。 帳單設定檔讀者和發票管理者可以檢視所有使用帳單設定檔支付的保留。 不過，他們無法對保留進行變更。
    如需詳細資訊，請參閱[帳單設定檔角色和工作](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks)。

### <a name="how-billing-administrators-view-or-manage-reservations"></a>計費管理員如何檢視或管理保留

1. 移至 [成本管理 + 計費]，然後在頁面的左側選取 [保留交易]。
2. 如果您有所需的計費權限，即可檢視和管理保留。 如果您沒有看到任何保留，請確定您已使用建立保留所用的 Azure AD 租用戶來登入。

## <a name="view-reservation-and-utilization-in-the-azure-portal"></a>檢視 Azure 入口網站中的保留和使用率

若要以「擁有者」或「讀者」身分檢視保留

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 移至[保留](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)。
3. 清單中會顯示您具備「擁有者」或「讀者」角色的所有保留。 每個保留都會顯示最後的已知使用率百分比。
4. 選取 [使用率百分比]，以查看使用量歷程記錄和詳細資料。 請參閱下列影片中的詳細資料。
   > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4sYwk] 

## <a name="get-reservations-and-utilization-using-apis-powershell-and-cli"></a>使用 API、PowerShell 和 CLI 取得保留和使用率

使用下列資源取得所有保留的清單：

- [API：保留訂單 - 清單](/rest/api/reserved-vm-instances/reservationorder/list)
- [PowerShell：保留訂單 - 清單](/powershell/module/azurerm.reservations/get-azurermreservationorder)
- [CLI：保留訂單 - 清單](/cli/azure/reservations/reservation-order#az-reservations-reservation-order-list)

您也可以使用保留執行個體使用方式 API 來取得[保留使用率](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)。 

## <a name="see-reservations-and-utilization-in-power-bi"></a>請參閱 Power BI 中的保留和使用率

Power BI 的使用者有兩個選項
- 內容套件：[耗用量深入解析 Power BI 內容套件](/power-bi/desktop-connect-azure-cost-management)中具有保留購買資料和使用率資料。 使用內容套件來建立您想要的報告。 
- 成本管理應用程式：針對預先建立的報告，使用[成本管理應用程式](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp)，可供您進一步進行自訂。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡

如果您有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

- [管理 Azure 保留](manage-reserved-vm-instance.md)。
- [了解隨用隨付訂用帳戶的保留使用量](understand-reserved-instance-usage.md)。
- [了解 Enterprise 註冊的保留項目使用量](understand-reserved-instance-usage-ea.md)。
- [了解 CSP 訂用帳戶的保留使用量](/partner-center/azure-reservations)。


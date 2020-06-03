---
title: 管理 Azure 保留
description: 了解如何管理 Azure 保留。
ms.service: cost-management-billing
author: bandersmsft
ms.reviewer: yashesvi
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: banders
ms.openlocfilehash: 1084e8687cc709b5e31a9ef2c903638dd3d8f62c
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "84019991"
---
# <a name="manage-reservations-for-azure-resources"></a>管理 Azure 資源的保留

購買 Azure 保留之後，您可能需要將保留套用至不同的訂用帳戶、變更可以管理保留的人員，或變更保留的範圍。 您也可以將保留分割成兩個保留，以將您購買的部分執行個體套用至另一個訂用帳戶。

如果您已購買 Azure 保留的 VM 執行個體，您可以變更保留的最佳化設定。 保留折扣可以套用至相同系列的 VM，或是您可以為特定 VM 大小保留資料中心容量。 而且，您應該嘗試將保留最佳化，使其充分使用。


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="reservation-order-and-reservation"></a>保留訂單和保留

當您購買保留時，會建立兩個物件：**保留訂單**和**保留**。

在購買時，保留訂單之下有一個保留。 分割、合併、部分退費或交換等動作會在 [保留訂單]  之下建立新的保留。

若要檢視保留訂單，請移至 [保留]  > 選取保留，然後按一下 [保留訂單識別碼]  。

![保留訂單詳細資料的範例，其中顯示保留訂單識別碼 ](./media/manage-reserved-vm-instance/reservation-order-details.png)

保留會繼承其保留訂單的權限。

## <a name="change-the-reservation-scope"></a>變更保留範圍

 保留折扣適用於符合保留且在保留範圍內執行的虛擬機器、SQL 資料庫、Azure Cosmos DB 或其他資源。 計費內容取決於購買此保留所用的訂用帳戶。

更新保留範圍：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [所有服務]   > [保留]  。
3. 選取保留。
4. 選取 [設定]   > [組態]  。
5. 變更範圍。

如果您從共用變更為單一範圍，您只能選取擁有者是您的訂用帳戶。 只能選取與保留屬於相同計費內容的訂用帳戶。

該範圍僅適用於採用隨用隨付費率的個別訂用帳戶 (供應項目 MS-AZR-0003P 或 MS-AZR-0023P、Enterprise 供應項目 MS-AZR-0017P 或 MS-AZR-0148P，或 CSP 訂用帳戶類型。

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>新增或變更可以管理保留的使用者

您可以透過將人員新增到保留順序或保留上的角色，來委派保留的管理。 依預設，放置保留順序的人員和帳戶管理員會具有保留順序和保留上的「擁有者」角色。

您可以從取得保留折扣的訂用帳戶，獨立管理保留訂單和保留的存取。 當您將管理保留訂單或保留的權限提供給其他人時，並不會將管理訂用帳戶的權限提供給他們。 同樣地，如果您將保留範圍內的訂用帳戶管理權限提供給其他人時，並不會將管理保留訂單或保留的權限提供給他們。

若要執行交換或退費，使用者必須具有保留訂單的存取權。 授與某人權限時，最好將權限授與保留訂單，而不是保留。


委派保留的存取管理：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [所有服務]   >  [保留]  來列出您具有存取權的保留。
3. 選取您想要將存取權委派給其他使用者的保留。
4. 選取 [存取控制 (IAM)]  。
5. 選取 [新增角色指派]   > [角色]   > [擁有者]  。 或是，如果您想要給予有限的存取權，可選取不同角色。
6. 輸入要新增為擁有者之使用者的電子郵件地址。
7. 選取使用者，然後選取 [儲存]  。

## <a name="split-a-single-reservation-into-two-reservations"></a>將單一保留分割成兩個保留

 購買保留內的多個資源執行個體之後，建議您將該保留中的執行個體指派至不同的訂用帳戶。 依預設，所有執行個體都具有一個範圍 - 單一訂用帳戶或共用。 例如，您購買了 10 個保留執行個體，並指定範圍為訂用帳戶 A。您現在要將 7 個保留的範圍變更為訂用帳戶 A，而剩下 3 個變更為訂用帳戶 B。分割保留可讓您分散執行個體，以進行更細微的管理。 您可以選擇共用範圍來簡化訂用帳戶的配置。 但是，基於成本管理或預算考量，您可以配置一些數量給特定的訂用帳戶。

 您可以透過 PowerShell、CLI 或 API 來將保留分成兩個保留。

### <a name="split-a-reservation-by-using-powershell"></a>使用 PowerShell 分割保留

1. 執行下列命令以取得保留訂單識別碼：

    ```powershell
    # Get the reservation orders you have access to
    Get-AzReservationOrder
    ```

2. 取得保留的詳細資料：

    ```powershell
    Get-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```

3. 將保留分割成兩個，並分散執行個體：

    ```powershell
    # Split the reservation. The sum of the reservations, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
4. 您可以執行下列命令來更新範圍：

    ```powershell
    Update-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="cancel-exchange-or-refund-reservations"></a>取消、交換保留或進行退費

您可以取消、交換保留或進行退費，但有某些限制。 如需詳細資訊，請參閱 [Azure 保留的自助式交換和退費](exchange-and-refund-azure-reservations.md)。

## <a name="change-optimize-setting-for-reserved-vm-instances"></a>變更保留 VM 執行個體的最佳化設定

 當您購買保留的 VM 執行個體時，您可以選擇執行個體大小的彈性或容量優先順序。 執行個體大小彈性會將保留項目折扣套用至同一個 [VM 大小群組](https://aka.ms/RIVMGroups)中的其他 VM。 容量優先順序會讓資料中心容量供您的部署優先使用。 此選項可讓您更加確信您能夠在需要時啟動 VM 執行個體。

根據預設，當保留範圍為共用時，執行個體大小彈性就會啟用。 資料中心容量不會針對 VM 部署設定優先權。

對於單一範圍的保留，您可以為了容量優先順序 (而不是為了 VM 執行個體大小彈性) 而將保留最佳化。

若要更新保留的最佳化設定：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [所有服務]   >  [保留]  。
3. 選取保留。
4. 選取 [設定]   > [組態]  。  
  ![顯示組態項目的範例](./media/manage-reserved-vm-instance/add-product03.png)
5. 變更**最佳化設定**。  
  ![顯示最佳化對象設定的範例](./media/manage-reserved-vm-instance/instance-size-flexibility-option.png)

## <a name="optimize-reservation-use"></a>將保留使用最佳化

只有持續使用資源，才可節省 Azure 保留。 當您購買保留時，必須針對基本上 100% 可能的資源使用，支付一或三年期的費用。 嘗試充分利用您的保留，盡可能省下更多成本。 下列各節說明如何監視保留並將其使用最佳化。

### <a name="view-reservation-use-in-the-azure-portal"></a>在 Azure 入口網站中檢視保留

在 Azure 入口網站中，有一種方式可檢視保留使用量。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 選取 [所有服務]   > [**保留**](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)，並記下保留的 [使用率 (%)]  。  
  ![顯示保留清單的影像](./media/manage-reserved-vm-instance/reservation-list.png)
3. 選取保留。
4. 檢閱一段時間的保留使用趨勢。  
  ![顯示保留使用的影像 ](./media/manage-reserved-vm-instance/reservation-utilization-trend.png)

### <a name="view-reservation-use-with-api"></a>透過 API 檢視保留

如果您是 Enterprise 合約 (EA) 客戶，您可以程式設計方式檢視您組織中之保留的使用方式。 您可透過使用量資料取得未使用的保留。 當您檢閱保留費用時，請記住，資料會分成實際成本和分攤成本。 實際成本會提供資料來調節您的每月帳單。 它也具有保留購買成本和保留套用詳細資料。 分攤成本與實際成本類似，不同之處在於保留使用量的有效價格會按比例計算。 未使用的保留時數會顯示在分攤成本資料中。 如需 EA 客戶使用量資料的詳細資訊，請參閱[取得 Enterprise 合約保留成本和使用量](understand-reserved-instance-usage-ea.md)。

對於其他訂用帳戶類型，可以使用 API[保留項目摘要 - 依保留訂單和保留列出的清單](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation)。

### <a name="optimize-your-reservation"></a>將您的保留最佳化

如果您發現貴組織的保留未充分運用：

- 請確定貴組織建立的虛擬機器符合保留上的 VM 大小。
- 請確認執行個體大小彈性已開啟。 如需詳細資訊，請參閱[管理保留 - 變更保留的 VM 執行個體的最佳化設定](#change-optimize-setting-for-reserved-vm-instances)。
- 變更要共用  的保留範圍，使它更廣泛地套用。 如需詳細資訊，請參閱[變更保留的範圍](#change-the-reservation-scope)。
- 請考慮交換未使用的數量。 如需詳細資訊，請參閱[取消和交換](#cancel-exchange-or-refund-reservations)。


## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure 保留項目，請參閱下列文章：

- [什麼是 Azure 的保留？](save-compute-costs-reservations.md)

購買服務方案：
- [預付具有 Azure 保留 VM 執行個體的虛擬機器](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [以 Azure SQL Database 保留容量預先支付 SQL 資料庫計算資源的費用](../../azure-sql/database/reserved-capacity-overview.md)
- [以 Azure Cosmos DB 保留容量預先支付 Azure Cosmos DB 資源的費用](../../cosmos-db/cosmos-db-reserved-capacity.md)

購買軟體方案：
- [從 Azure 保留預付 Red Hat 軟體方案](../../virtual-machines/linux/prepay-rhel-software-charges.md)
- [從 Azure 保留預付 SUSE 軟體方案](../../virtual-machines/linux/prepay-suse-software-charges.md)

了解折扣與使用量：
- [了解 VM 保留折扣的套用方式](../manage/understand-vm-reservation-charges.md)
- [了解如何套用 Red Hat Enterprise Linux 軟體方案折扣](understand-rhel-reservation-charges.md)
- [了解如何套用 SUSE Linux Enterprise 軟體方案折扣](understand-suse-reservation-charges.md)
- [了解其他保留折扣的套用方式](understand-reservation-charges.md)
- [了解隨用隨付訂用帳戶的保留使用量](understand-reserved-instance-usage.md)
- [了解 Enterprise 註冊的保留項目使用量](understand-reserved-instance-usage-ea.md)
- [Windows 軟體成本不包含在 Reservations 內](reserved-instance-windows-software-costs.md)

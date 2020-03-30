---
title: 軟體計畫的預付款 - Azure 預訂
description: 瞭解如何為軟體計畫預付費用，以節省您即用即付成本的費用。
author: bandersmsft
manager: yashesvi
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 3bb7a62433993f1af26b1ce8bcb4ed258c34623c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75973141"
---
# <a name="prepay-for-azure-software-plans"></a>預付 Azure 軟體方案

在 Azure 中預付費 SUSE 和 RedHat 軟體使用方式時，可以節省即用即付成本。 折扣僅適用于 SUSE 和 RedHat 儀錶，不適用於虛擬機器使用方式。 您可以單獨購買虛擬機器的預訂，以節省更多費用。

您可以在 Azure 門戶中購買 SUSE 和 RedHat 軟體計畫。 若要購買方案：

- 您必須具有至少一個企業或個人訂閱的擁有者角色，並帶有即用即付定價。
- 針對企業訂用帳戶，必須在 [EA 入口網站](https://ea.azure.com/)中啟用 [新增保留執行個體]**** 選項。 如果禁用該設置，則必須是訂閱的 EA 管理員。
- 對於雲解決方案供應商 （CSP） 計畫，管理員代理或銷售代理可以購買軟體計畫。

## <a name="buy-a-software-plan"></a>購買軟體計畫

1. 登錄到 Azure 門戶並轉到["預訂](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)"。
2. 按一下"**添加**"，然後選擇要購買的軟體計畫。
填寫必要欄位。 任何與您購買的產品的屬性相匹配的 SUSE Linux VM 或 RedHat VM 都會獲得折扣。 取得折扣的實際部署數目取決於選取的範圍和數量。
3. 選取一個訂用帳戶。 它用來支付計畫的費用。
訂閱付款條件按預訂的前期費用收費。 訂閱類型必須是企業協定（產品/服務編號：MS-AZR-0017P 或 MS-AZR-0148P）或具有即用即付定價（產品/服務編號：MS-AZR-0003P 或 MS-AZR-0023P）的個人協定。
    - 針對企業訂用帳戶，費用會從註冊的承諾用量金額餘額扣除或作為超額部分收費。
    - 對於具有即用即付定價的個人訂閱，費用將按訂閱的信用卡或發票付款條件計費。
4. 選取範圍。 此範圍可以涵蓋一個訂用帳戶或多個訂用帳戶 (共用範圍)。
    - 單個訂閱 - 計畫折扣應用於訂閱中的匹配使用方式。
    - 共用 - 計畫折扣應用於計費上下文中任何訂閱中的匹配實例。 對於企業客戶，計費上下文是註冊，包括註冊中的所有訂閱。 對於具有即用即付定價客戶的單個計畫，計費上下文是所有由帳戶管理員創建的即用即付定價訂閱的單個計畫。
5. 選擇產品以選擇 VM 大小和映射類型。 折扣僅適用于所選的 VM 大小。
6. 選擇一年或三年期限。
7. 選擇數量，即可獲取計費折扣的預付費 VM 實例數。
8. 將產品添加到購物車中，查看和購買。

預訂折扣將自動應用於您預付的軟體儀錶。 VM 計算費用不在計畫範圍內。 您可以單獨購買 VM 預留。

## <a name="discount-applies-to-different-suse-vm-sizes"></a>折扣適用于不同的 SUSE VM 大小

與保留的 VM 實例一樣，SUSE Linux 計畫提供實例大小靈活性。 即使您部署的 VM 大小與您購買的 SUSE 計畫不同，您的折扣也會適用。 有關詳細資訊，請參閱[瞭解如何應用軟體計畫折扣](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)。

## <a name="redhat-plan-discount"></a>RedHat 計畫折扣

計畫僅適用于紅帽企業 Linux 虛擬機器。 折扣不適用於紅帽企業 Linux SAP HANA VM 或紅帽企業 Linux SAP 業務應用 VM。

RedHat 計畫折扣僅適用于您在購買時選擇的 VM 大小。 RHEL 計畫購買後不能退款或交換。


## <a name="cancellation-and-exchanges-not-allowed"></a>不允許取消和交換

您無法取消或交換您購買的 SUSE 或 RedHat 計畫。 檢查您的使用量，以確定您購買適當的方案。 有關確定購買內容的説明，請參閱[瞭解如何應用軟體計畫折扣](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有疑問或需要幫助，[請創建支援請求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>後續步驟

若要了解如何管理保留項目，請參閱[管理 Azure 保留項目](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)。

如需詳細資訊，請參閱下列文章：

- [什麼是 Azure 保留項目？](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [管理 Azure 中的預留](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [了解 SUSE 保留折扣的套用方式](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)
- [了解隨用隨付訂用帳戶的保留使用量](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [了解 Enterprise 註冊的保留項目使用量](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)

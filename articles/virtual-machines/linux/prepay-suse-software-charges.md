---
title: 預付軟體方案-Azure 保留
description: 瞭解如何預付軟體方案，以節省隨用隨付成本的費用。
author: bandersmsft
manager: yashesvi
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: banders
ms.openlocfilehash: 5a4f7de0cacb17087e346123f53dad45ae1de357
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602617"
---
# <a name="prepay-for-azure-software-plans"></a>預付 Azure 軟體方案

當您在 Azure 中預付 SUSE 和 RedHat 軟體使用方式時，您可以透過隨用隨付成本來節省成本。 折扣僅適用于 SUSE 和 RedHat 計量，而不適用於虛擬機器使用量。 您可以個別購買虛擬機器的保留空間，以節省更多費用。

您可以在 Azure 入口網站中購買 SUSE 和 RedHat 軟體方案。 若要購買方案：

- 您必須擁有至少一個具有隨用隨付定價之企業或個別訂用帳戶的擁有者角色。
- 針對企業訂用帳戶，必須在 [EA 入口網站](https://ea.azure.com/)中啟用 [新增保留執行個體]  選項。 如果設定已停用，您必須是訂用帳戶的 EA 系統管理員。
- 針對雲端解決方案提供者 (CSP) 程式，系統管理員專員或銷售專員可以購買軟體方案。

## <a name="buy-a-software-plan"></a>購買軟體方案

1. 登入 Azure 入口網站並移至 [ [保留](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)]。
2. 按一下 [ **新增** ]，然後選取您想要購買的軟體方案。
填寫必要欄位。 任何與您購買的屬性相符的 SUSE Linux VM 或 RedHat VM 都會獲得折扣。 取得折扣的實際部署數目取決於選取的範圍和數量。
3. 選取一個訂用帳戶。 它是用來支付方案的費用。
訂用帳戶付款條件會收取保留的預付成本。 訂用帳戶類型必須是 Enterprise 合約 (供應專案號碼： MS-AZR-0003P->ms-azr-0017p 或 MS-AZR-0003P-Ms-azr-0148p) 或依隨用隨付定價的個別合約 (供應專案號碼： MS-MS-AZR-0003P-Ms-azr-0003p 或 MS-MS-AZR-0003P-Ms-azr-0023p) 。
    - 若為企業訂用帳戶，費用會從註冊的 Azure 預付款扣除 (先前稱為預付金) 餘額或收取超額部分的費用。
    - 針對具有隨用隨付定價的個別訂用帳戶，費用會以訂用帳戶的信用卡或發票付款方法計費。
4. 選取範圍。 此範圍可以涵蓋一個訂用帳戶或多個訂用帳戶 (共用範圍)。
    - 單一訂用帳戶-方案折扣會套用至訂用帳戶中的相符使用量。
    - 共用-方案折扣會套用至計費內容中任何訂用帳戶的相符實例。 對於企業客戶而言，計費內容是註冊，並包含註冊中的所有訂用帳戶。 針對隨用隨付定價客戶的個別方案，計費內容是由帳戶管理員所建立之隨用隨付定價訂用帳戶的所有個別方案。
    - 單一資源群組-只會將保留折扣套用至所選資源群組中的相符資源。
5. 選取產品以選擇 VM 大小和映射類型。 折扣僅適用于選取的 VM 大小。
6. 選取一年或三年期。
7. 選擇數量，也就是可取得帳單折扣的預付 VM 實例數目。
8. 將產品新增至購物車、審查及購買。

保留折扣會自動套用至您預先支付的軟體計量。 方案不會涵蓋 VM 計算費用。 您可以另外購買 VM 保留。

## <a name="discount-applies-to-different-suse-vm-sizes"></a>折扣適用于不同的 SUSE VM 大小

如同保留的 VM 實例，SUSE Linux 方案提供實例大小的彈性。 即使您部署的 VM 大小與您所購買的 SUSE 方案不同，您的折扣也適用。 如需詳細資訊，請參閱 [瞭解軟體方案折扣的套用方式](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)。

## <a name="redhat-plan-discount"></a>RedHat 方案折扣

方案僅適用于 Red Hat Enterprise Linux 的虛擬機器。 折扣不適用於 RedHat Enterprise Linux SAP Hana Vm 或 RedHat Enterprise Linux SAP Business Apps Vm。

RedHat 方案折扣只適用于您在購買時選取的 VM 大小。 在購買後，無法退款或交換 RHEL 方案。


## <a name="cancellation-and-exchanges-not-allowed"></a>不允許取消和交換

您無法取消或交換您所購買的 SUSE 或 RedHat 方案。 檢查您的使用量，以確定您購買適當的方案。 如需識別所要購買之產品的說明，請參閱 [瞭解軟體方案折扣的套用方式](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>後續步驟

若要瞭解如何管理保留，請參閱 [管理 Azure 保留](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)專案。

如需詳細資訊，請參閱下列文章：

- [什麼是 Azure 保留項目？](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [管理 Azure 中的保留](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [了解 SUSE 保留折扣的套用方式](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)
- [了解隨用隨付訂用帳戶的保留使用量](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [了解 Enterprise 註冊的保留項目使用量](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)

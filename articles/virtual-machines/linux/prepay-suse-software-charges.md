---
title: 預付軟體方案-Azure 保留專案
description: 瞭解如何預付軟體方案，以節省隨用隨付費用的金錢。
author: bandersmsft
manager: yashesvi
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: banders
ms.openlocfilehash: a527b75d376f05ab6190187b7a03d6da775055ab
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81759279"
---
# <a name="prepay-for-azure-software-plans"></a>預付 Azure 軟體方案

當您在 Azure 中預付 SUSE 和 RedHat 軟體使用量時，可以節省隨用隨付成本的費用。 折扣僅適用于 SUSE 和 RedHat 計量，而不適用於虛擬機器的使用量。 您可以單獨購買虛擬機器的保留，以節省額外費用。

您可以在 Azure 入口網站中購買 SUSE 和 RedHat 軟體方案。 若要購買方案：

- 您必須至少擁有一個企業或個別訂用帳戶的擁有者角色，且具有隨用隨付定價。
- 針對企業訂用帳戶，必須在 [EA 入口網站](https://ea.azure.com/)中啟用 [新增保留執行個體]  選項。 如果設定已停用，您必須是訂用帳戶的 EA 系統管理員。
- 針對雲端解決方案提供者（CSP）計畫，管理員代理程式或銷售代理人可以購買軟體方案。

## <a name="buy-a-software-plan"></a>購買軟體方案

1. 登入 Azure 入口網站並移至 [[保留](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)]。
2. 按一下 [**新增**]，然後選取您要購買的軟體方案。
填寫必要欄位。 符合您所購買之屬性的任何 SUSE Linux VM 或 RedHat VM 都會取得折扣。 取得折扣的實際部署數目取決於選取的範圍和數量。
3. 選取一個訂用帳戶。 它是用來支付方案的費用。
訂用帳戶付款方法會收取保留的預付成本。 訂用帳戶類型必須是 Enterprise 合約（供應專案號碼： MS-AZR-0017P-Ms-azr-0017p 或 MS-MS-AZR-0017P-Ms-azr-0148p）或個別合約與隨用隨付定價（供應專案號碼： MS-MS-AZR-0017P-Ms-azr-0003p 或 MS-MS-AZR-0017P-Ms-azr-0023p）。
    - 針對企業訂用帳戶，費用會從註冊的承諾用量金額餘額扣除或作為超額部分收費。
    - 針對使用隨用隨付定價的個別訂用帳戶，費用會依訂用帳戶的信用卡或發票付款方法計費。
4. 選取範圍。 此範圍可以涵蓋一個訂用帳戶或多個訂用帳戶 (共用範圍)。
    - 單一訂用帳戶-方案折扣適用于訂用帳戶中的相符使用量。
    - 共用-方案折扣適用于帳單內容中任何訂用帳戶的相符實例。 對於企業客戶，計費內容是註冊，並包含註冊中的所有訂用帳戶。 對於隨用隨付定價客戶的個別方案，計費內容是由帳戶管理員所建立的隨用隨付定價訂用帳戶的所有個別方案。
    - 單一資源群組-只會將保留折扣套用至所選資源群組中的相符資源。
5. 選取產品以選擇 VM 大小和映射類型。 折扣僅適用于選取的 VM 大小。
6. 選取一年或三年期。
7. 選擇 [數量]，這是可取得計費折扣的預付 VM 實例數目。
8. 將產品加入購物車、審查和購買。

保留折扣會自動套用至您預先支付的軟體計量。 方案不涵蓋 VM 計算費用。 您可以分別購買 VM 保留區。

## <a name="discount-applies-to-different-suse-vm-sizes"></a>折扣適用于不同的 SUSE VM 大小

如同保留的 VM 實例，SUSE Linux 方案提供實例大小彈性。 即使您部署的 VM 大小與您購買的 SUSE 方案不同，您的折扣也適用。 如需詳細資訊，請參閱[瞭解如何套用軟體方案折扣](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)。

## <a name="redhat-plan-discount"></a>RedHat 方案折扣

方案僅適用于 Red Hat Enterprise Linux 的虛擬機器。 折扣不適用於 RedHat Enterprise Linux SAP Hana Vm 或 RedHat Enterprise Linux SAP Business Apps Vm。

RedHat 方案折扣僅適用于您在購買時選取的 VM 大小。 您無法在購買後退款或交換 RHEL 方案。


## <a name="cancellation-and-exchanges-not-allowed"></a>不允許取消和交換

您無法取消或交換您購買的 SUSE 或 RedHat 方案。 檢查您的使用量，以確定您購買適當的方案。 如需識別要購買之內容的協助，請參閱[瞭解如何套用軟體方案折扣](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>後續步驟

若要瞭解如何管理保留，請參閱[管理 Azure 保留](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)。

如需詳細資訊，請參閱下列文章：

- [什麼是 Azure 保留項目？](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [管理 Azure 中的保留](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [了解 SUSE 保留折扣的套用方式](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)
- [了解隨用隨付訂用帳戶的保留使用量](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [了解 Enterprise 註冊的保留項目使用量](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)

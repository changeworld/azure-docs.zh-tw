---
title: 使用保留容量來節省 Azure App Service 費用
description: 了解如何使用保留容量來節省 Azure App Service 隔離式方案戳記費用的成本。
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 21c4b7de0324abd2755644265bec61de5e16724f
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199410"
---
# <a name="save-costs-for-azure-app-service-isolated-stamp-fee-with-reserved-capacity"></a>使用保留容量來節省 Azure App Service 隔離式方案戳記費用的成本

您可以藉由承諾會在三年期間內購買保留來獲得戳記使用量，以節省 Azure App Service 隔離式方案戳記費用的成本。 若要購買隔離式方案戳記費用保留容量，您必須選擇將會用來部署戳記的 Azure 區域，以及要購買的戳記數目。

當您購買保留時，與保留屬性相符的隔離式方案戳記費用使用量，就不會再以隨用隨付費率收費。 保留會自動套用到符合保留容量範圍和區域的隔離式方案戳記數目。 您不需要將保留指派給隔離式方案戳記。 保留不會套用至背景工作角色，因此任何其他與戳記相關聯的資源也會分開收費。

當保留容量到期時，隔離式方案戳記會繼續執行，但會按隨用隨附費率收費。 保留不會自動更新。

## <a name="determine-the-right-reservation-to-purchase"></a>決定要購買的正確保留

購買保留就表示您承諾會在接下來的三年中使用保留數量。 請查看您的使用量資料，以決定您一直在使用並可能會在未來使用的 App Service 隔離式方案戳記數目。

此外，也請確定您已了解隔離式方案戳記會如何發出 Linux 或 Windows 計量。

- 根據預設，空的隔離式方案戳記會發出 Windows 戳記計量。 例如，未部署任何背景工作角色。 如果 Windows 背景工作角色部署在戳記上，則戳記會繼續發出此計量。
- 如果您部署 Linux 背景工作角色，計量會變更為 Linux 戳記計量。
- 在同時部署了 Linux 和 Windows 背景工作角色的情況下，戳記會發出 Windows 計量。

因此，戳記計量可能會在戳記的生命週期內，於 Windows 和 Linux 之間變來變去。

如果您在戳記上有一或多個 Windows 背景工作角色，請購買 Windows 戳記保留。 當您打算只  讓戳記上有 Linux 背景工作角色時，才應該購買 Linux 戳記保留。

## <a name="buy-isolated-stamp-reserved-capacity"></a>購買隔離式方案戳記保留容量

您可以在 [Azure 入口網站](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D)購買隔離式方案戳記保留容量。 保留的付款方式可為[預先付款或每月付款](monthly-payments-reservations.md)。 若要購買保留容量，您必須擁有至少一個企業訂用帳戶的擁有者角色，或具有隨用隨付費率的個別訂用帳戶的擁有者角色。

- 針對企業訂用帳戶，必須在 [EA 入口網站](https://ea.azure.com/)中啟用 [新增保留執行個體]  選項。 或者，如果該設定已停用，則您必須是 EA 系統管理員。
- 若為雲端解決方案提供者 (CSP) 方案，則只有系統管理員代理人或銷售人員可以購買 SQL 資料倉儲保留容量。

**若要購買：**

1. 移至 [Azure 入口網站](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D)。
1. 選取一個訂用帳戶。 使用 [訂用帳戶]  清單來選擇用來支付保留容量的訂用帳戶。 訂用帳戶的付款方式為收取保留容量的費用。 訂用帳戶類型必須是 Enterprise 合約 (供應項目號碼：MS-AZR-0017P 或 MS-AZR-0148P) 或隨用隨付 (供應項目號碼：MS-AZR-0003P 或 MS-AZR-0023P) 或 CSP 訂用帳戶。
    - 針對企業訂用帳戶，費用會從註冊的承諾用量金額餘額扣除或作為超額部分收費。
    - 針對隨用隨付訂用帳戶，費用是透過訂用帳戶的信用卡或發票付款方式收取。
1. 選取 [範圍]  以選擇訂用帳戶範圍。
    - **單一資源群組範圍** — 只會將保留折扣套用至所選資源群組中的相符資源。
    - **單一訂用帳戶範圍** — 會將保留折扣套用至所選訂用帳戶中的相符資源。
    - **共用範圍** — 會將保留折扣套用至計費內容中合格訂用帳戶的相符資源。 針對 Enterprise 合約客戶，計費內容為註冊。 針對使用隨用隨付費率的個別訂用帳戶，計費範圍是帳戶管理員所建立的所有合格訂用帳戶。
1. 選取 [區域]  以選擇保留容量涵蓋的 Azure 區域，並將保留新增至購物車。
1. 選取隔離式方案類型，然後按一下 [選取]  。  
    ![範例 ](./media/prepay-app-service-isolated-stamp/app-service-isolated-stamp-select.png)
1. 輸入要保留的 App Service 隔離式方案戳記數量。 例如，數量為三會在一個區域內提供三個保留戳記。 按一下 **[下一步**檢閱 + 購買]。
1. 檢閱並按一下 [立即購買]  。

購買之後，請移至[保留](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)以檢視購買狀態，並隨時加以監視。

## <a name="cancel-exchange-or-refund-reservations"></a>取消、交換保留或進行退費

您可以取消、交換保留或進行退費，但有某些限制。 如需詳細資訊，請參閱 [Azure 保留的自助式交換和退費](exchange-and-refund-azure-reservations.md)。

## <a name="discount-application-shown-in-usage-data"></a>使用量資料中顯示的折扣套用

對於獲得保留折扣的使用量，您的使用量資料有效價格會是零。 使用量資料會顯示每個保留中每個戳記執行個體的保留折扣。

如果您是 Enterprise 合約 (EA) 客戶且需要保留折扣在使用量資料中顯示方式的詳細資訊，請參閱[取得 Enterprise 合約保留成本和使用量](understand-reserved-instance-usage-ea.md)。 否則，請參閱[了解個別訂用帳戶 (採用隨用隨付費率) 的 Azure 保留使用量](understand-reserved-instance-usage.md)。

## <a name="next-steps"></a>後續步驟

- 若要深入了解 Azure 保留項目，請參閱下列文章：
  - [什麼是 Azure 保留項目？](save-compute-costs-reservations.md)
  - [了解 Azure App Service 隔離式方案戳記保留折扣的套用方式](reservation-discount-app-service-isolated-stamp.md)
  - [了解 Enterprise 註冊的保留項目使用量](understand-reserved-instance-usage-ea.md)

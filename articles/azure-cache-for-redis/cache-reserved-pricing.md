---
title: 使用保留容量預付計算-Azure Cache for Redis
description: 預付具有保留容量的 Azure Cache for Redis 計算資源
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 9931dd9acb51e74c34a57b0475bca79fb9ba79cf
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184173"
---
# <a name="prepay-for-azure-cache-for-redis-compute-resources-with-reserved-capacity"></a>預付具有保留容量的 Azure Cache for Redis 計算資源

相較于隨用隨付價格，Azure Cache for Redis 現在可協助您節省預付的計算資源成本。 使用 Azure Cache for Redis 保留容量，您可以預先承諾一或三年期的快取，以取得計算成本的大量折扣。 若要購買 Azure Cache for Redis 保留容量，您必須指定 Azure 區域、服務層和期限。

您不需要將保留指派給特定的 Azure Cache for Redis 實例。 已在執行中的 Azure Cache for Redis 或剛部署的將會自動獲得保留定價的優點，而不是保留的快取大小。 透過購買保留，您會預付為期一或三年的計算費用。 當您購買保留專案時，與保留屬性相符的 Azure Cache for Redis 計算費用就不會再以隨用隨付費率計費。 保留並未涵蓋與快取相關聯的網路或儲存體費用。 在保留期限結束時，帳單權益會過期，而 Azure Cache for Redis 會以隨用隨付價格計費。 保留不會自動更新。 如需定價資訊，請參閱 [Azure Cache for Redis 保留容量](https://azure.microsoft.com/pricing/details/cache)供應專案。

您可以在 [Azure 入口網站](https://portal.azure.com/)中購買 Azure Cache for Redis 的保留容量。 若要購買保留容量：

* 您必須是至少一個具有隨用隨付費率的企業或個別訂用帳戶的擁有者角色。
* 針對企業訂用帳戶，必須在 [EA 入口網站](https://ea.azure.com/)中啟用 **新增保留執行個體**。 或者，如果該設定已停用，則您必須是訂用帳戶上的 EA 系統管理員。
* 針對雲端解決方案提供者 (CSP) 方案，只有系統管理員專員或銷售專員可以購買 Azure Cache for Redis 保留容量。

如需企業客戶和隨用隨付客戶如何針對保留購買收費的詳細資訊，請參閱 [瞭解您企業註冊的 azure 保留使用量](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) ，以及 [瞭解隨用隨付訂用帳戶的 azure 保留使用量](../cost-management-billing/reservations/understand-reserved-instance-usage.md)。


## <a name="determine-the-right-cache-size-before-purchase"></a>在購買前判斷正確的快取大小

保留大小應該以特定區域內現有或即將部署的快取所使用的記憶體大小總數為基礎，並使用相同的服務層級。

例如，假設您正在執行兩個快取-一個為 13 GB，另一個為 26 GB。 您至少需要一年的時間。 此外，假設您想要將現有的 13 GB 快取調整為每個月 26 GB，以符合季節性需求，然後再調整回來。 在此情況下，您可以購買1個 P2 快取和1個 P3 快取或3個在一年保留的 P2 快取，以最大化節省成本。 您將會收到保留的快取記憶體總量的折扣，與您在快取中配置該數量的方式無關。


## <a name="buy-azure-cache-for-redis-reserved-capacity"></a>購買 Azure Cache for Redis 保留容量

您可以在 [Azure 入口網站](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/)中購買保留的 VM 實例。 保留的付款方式可為[預先付款或每月付款](../cost-management-billing/reservations/prepare-buy-reservation.md)。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 選取 [所有服務] > [保留]。
3. 選取 [ **新增** ]，然後在 [購買保留] 窗格中，選取 [ **Azure Cache for Redis** ] 為您的快取購買新的保留。
4. 填寫必要欄位。 符合所選屬性的現有或新資料庫符合取得保留容量折扣的資格。 取得折扣的 Azure Cache for Redis 實例實際數目取決於選取的範圍和數量。


![保留定價總覽](media/cache-reserved-pricing/cache-reserved-price.png)


下表說明必要的欄位。

| 欄位 | 描述 |
| :------------ | :------- |
| 訂用帳戶   | 用來支付 Azure Cache for Redis 保留容量保留的訂用帳戶。 訂用帳戶上的付款方法會收取 Azure Cache for Redis 保留容量保留的預付成本。 訂用帳戶類型必須是 enterprise 合約 (供應專案號碼： MS-AZR-0003P->ms-azr-0017p 或 MS-AZR-0003P-Ms-azr-0148p) 或具有隨用隨付定價的個別合約 (供應專案號碼： MS-MS-AZR-0003P-Ms-azr-0003p 或 MS-MS-AZR-0003P-Ms-azr-0023p) 。 針對企業訂用帳戶，費用會從註冊的承諾用量金額餘額扣除或作為超額部分收費。 針對具有隨用隨付定價的個別訂用帳戶，費用會以訂用帳戶的信用卡或發票付款方法計費。
| 影響範圍 | 保留項目範圍可以涵蓋一個訂用帳戶或多個訂用帳戶 (共用範圍)。 如果您選取： </br></br> **共用**，保留折扣會套用至計費內容內任何訂用帳戶中執行的 Azure Cache for Redis 實例。 針對企業客戶，共用範圍是註冊，並包含註冊中的所有訂用帳戶。 針對隨用隨付客戶，共用範圍是帳戶系統管理員所建立的所有隨用隨付訂用帳戶。</br></br> **單一訂** 用帳戶，保留折扣會套用到此訂用帳戶中的 Azure Cache for Redis 實例。 </br></br> **單一資源群組**，保留折扣會套用至所選訂用帳戶中的 Azure Cache for Redis 實例，以及該訂用帳戶內選取的資源群組。
| 區域 | Azure Cache for Redis 保留容量保留所涵蓋的 Azure 區域。
| 定價層 | Azure Cache for Redis 伺服器的服務層級。
| 詞彙 | 一年或三年
| 數量 | Azure Cache for Redis 保留容量保留內所購買的計算資源數量。 數量是所選 Azure 區域和服務層中所要保留的快取數目，將會獲得帳單折扣。 例如，如果您正在執行或計畫在美國東部區域中執行具有總快取容量的 Azure Cache for Redis 伺服器，則您會指定數量等於 26 GB 的數量，以將所有快取的效益最大化。 這可能是1個 P3 或2個 P2 快取。

## <a name="cancel-exchange-or-refund-reservations"></a>取消、交換保留或進行退費

您可以取消、交換保留或進行退費，但有某些限制。 如需詳細資訊，請參閱 [Azure 保留的自助式交換和退費](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)。

## <a name="cache-size-flexibility"></a>快取大小彈性

快取大小彈性可協助您在服務層級和區域內擴大或縮小，而不會失去保留容量優勢。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡

如果您有問題或需要協助，請[建立支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>後續步驟

保留折扣會自動套用至符合保留範圍和屬性的 Azure Cache for Redis 實例。 您可以透過 Azure 入口網站、PowerShell、Azure CLI 或 API 來更新保留容量的範圍。

*  若要瞭解如何將保留容量折扣套用至 Azure Cache for Redis，請參閱 [瞭解 Azure 保留折扣](../cost-management-billing/reservations/understand-azure-cache-for-redis-reservation-charges.md)

* 若要深入了解 Azure 保留項目，請參閱下列文章：

    * [什麼是 Azure 保留項目？](../cost-management-billing/reservations/save-compute-costs-reservations.md)
    * [管理 Azure 保留項目](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
    * [了解 Azure 保留折扣](../cost-management-billing/reservations/understand-reservation-charges.md)
    * [了解隨用隨付訂用帳戶的保留使用量](../cost-management-billing/reservations/understand-reservation-charges-mysql.md)
    * [了解 Enterprise 註冊的保留項目使用量](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
    * [合作夥伴中心雲端解決方案提供者 (CSP) 計畫中的 Azure 保留項目](/partner-center/azure-reservations)
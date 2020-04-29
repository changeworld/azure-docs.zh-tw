---
title: 預付保留容量的計算-Azure Cache for Redis
description: 預付 Azure Cache for Redis 使用保留容量的計算資源
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: aded023c9f4c045f612e33d32c1e3ac71afddf02
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77530298"
---
# <a name="prepay-for-azure-cache-for-redis-compute-resources-with-reserved-capacity"></a>預付 Azure Cache for Redis 使用保留容量的計算資源

相較于隨用隨付價格，Azure Cache for Redis 現在可協助您節省還要划算的計算資源費用。 使用 Azure Cache for Redis 保留容量時，您會在快取中提前一或三年期的預付承諾，以取得計算成本的大量折扣。 若要購買 Azure Cache for Redis 保留容量，您需要指定 Azure 區域、服務層和期限。

您不需要將保留指派給特定的 Azure Cache for Redis 實例。 已執行的 Azure Cache for Redis 或新部署的快取，將會自動獲得保留的定價權益，最高可達保留的快取大小。 透過購買保留，您會預付為期一或三年的計算費用。 一旦您購買保留，符合保留屬性的 Azure Cache for Redis 計算費用就不再以隨用隨付費率計費。 保留不會涵蓋與快取相關聯的網路或儲存體費用。 在保留期限結束時，計費權益會到期，而 Azure Cache for Redis 會以隨用隨付價格計費。 保留不會自動更新。 如需定價資訊，請參閱[Azure Cache For Redis 保留容量](https://azure.microsoft.com/pricing/details/cache)供應專案。

您可以在[Azure 入口網站](https://portal.azure.com/)中購買 Azure Cache for Redis 保留容量。 若要購買保留容量：

* 您至少必須是一個企業或個別訂用帳戶的擁有者角色，且具有隨用隨付費率。
* 針對企業訂用帳戶，必須在 [EA 入口網站](https://ea.azure.com/)中啟用**新增保留執行個體**。 或者，如果該設定已停用，則您必須是訂用帳戶上的 EA 系統管理員。
* 針對雲端解決方案提供者（CSP）計畫，只有系統管理員代理程式或銷售代理人可以購買 Azure Cache for Redis 保留容量。

如需企業客戶和預付型方案客戶如何支付保留購買費用的詳細資訊，請參閱[瞭解 enterprise 註冊的 azure 保留使用量](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)和[瞭解隨用隨付訂用帳戶的 azure 保留使用量](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)。


## <a name="determine-the-right-cache-size-before-purchase"></a>在購買前判斷正確的快取大小

保留大小應根據特定區域內現有或即將部署的快取所使用的總計算量，並使用相同的服務層級。

例如，假設您執行的是一個一般用途、第5代– 32 vCore 快取，以及兩個記憶體優化、第5代– 16 vCore 快取。 此外，假設您計畫在下個月部署其他一般用途第5代– 32 vCore 資料庫伺服器，以及一個記憶體優化、第5代– 16 vCore 資料庫伺服器。 假設您知道您至少需要一年的這些資源。 在此情況下，您應該購買64（2x32）虛擬核心，單一資料庫一般目的-第5代和48（2x16 + 16） vCore 1 年保留，適用于單一資料庫記憶體優化-第5代


## <a name="buy-azure-cache-for-redis-reserved-capacity"></a>購買 Azure Cache for Redis 保留容量

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 選取 [**所有服務** > ] [**保留**]。
3. 選取 [**新增**]，然後在 [購買保留] 窗格中，選取 [ **Azure Cache for Redis** ] 來為您的快取購買新的保留。
4. 填寫必要欄位。 符合您所選屬性的現有或新資料庫，可以取得保留容量折扣。 取得折扣的 Azure Cache for Redis 實例實際數目取決於選取的範圍和數量。


![保留定價的總覽](media/cache-reserved-pricing/cache-reserved-price.png)


下表描述必要的欄位。

| 欄位 | 描述 |
| :------------ | :------- |
| 訂用帳戶   | 用來支付 Azure Cache for Redis 保留容量的訂用帳戶。 訂用帳戶的付款方法會針對 Azure Cache for Redis 保留容量保留期收取預付費用。 訂用帳戶類型必須是 enterprise 合約（供應專案號碼： MS-AZR-0017P-Ms-azr-0017p 或 MS-MS-AZR-0017P-Ms-azr-0148p）或具有隨用隨付定價的個別合約（供應專案號碼： MS-MS-AZR-0017P-Ms-azr-0003p 或 MS-MS-AZR-0017P-Ms-azr-0023p）。 針對企業訂用帳戶，費用會從註冊的承諾用量金額餘額扣除或作為超額部分收費。 針對使用隨用隨付定價的個別訂用帳戶，費用會依訂用帳戶的信用卡或發票付款方法計費。
| 影響範圍 | 保留項目範圍可以涵蓋一個訂用帳戶或多個訂用帳戶 (共用範圍)。 如果您選取： </br></br> **共用**，保留折扣會套用至計費內容內任何訂用帳戶中執行的 Azure Cache for Redis 實例。 針對企業客戶，共用範圍是註冊，並包含註冊中的所有訂用帳戶。 針對隨用隨付客戶，共用範圍是帳戶系統管理員所建立的所有隨用隨付訂用帳戶。</br></br> **單一訂**用帳戶，保留折扣會套用至此訂用帳戶中的 Azure Cache for Redis 實例。 </br></br> **單一資源群組**，保留折扣會套用至所選訂用帳戶中的 Azure Cache for Redis 實例，以及該訂用帳戶內選取的資源群組。
| 區域 | Azure Cache for Redis 保留容量保留所涵蓋的 Azure 區域。
| 定價層 | Azure Cache for Redis 伺服器的服務層級。
| 詞彙 | 一年或三年
| 數量 | 在 Azure Cache for Redis 保留容量保留內購買的計算資源量。 數量是所選 Azure 區域和服務層中，已保留的數個快取，將會取得帳單折扣。 例如，如果您正在執行或打算在美國東部區域中，以 26 GB 的總快取容量來執行 Azure Cache for Redis 伺服器，則您會將 quantity 指定為26，以將所有快取的權益最大化。

## <a name="cancel-exchange-or-refund-reservations"></a>取消、交換保留或進行退費

您可以取消、交換保留或進行退費，但有某些限制。 如需詳細資訊，請參閱[Azure 保留的自助交換和退款](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund)。

## <a name="cache-size-flexibility"></a>快取大小彈性

快取大小彈性可協助您在服務層級和區域內相應增加或相應減少，而不會失去保留容量權益。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡

如果您有問題或需要協助，請[建立支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>後續步驟

保留折扣會自動套用至符合保留範圍和屬性的 Azure Cache for Redis 實例。 您可以透過 Azure 入口網站、PowerShell、Azure CLI 或 API 來更新保留容量的範圍。

*  若要瞭解如何將保留容量折扣套用至 Azure Cache for Redis，請參閱[瞭解 azure 保留折扣](../cost-management-billing/reservations/understand-azure-cache-for-redis-reservation-charges.md)

* 若要深入了解 Azure 保留項目，請參閱下列文章：

    * [什麼是 Azure 保留專案？](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
    * [管理 Azure 保留項目](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
    * [了解 Azure 保留折扣](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
    * [了解隨用隨付訂用帳戶的保留使用量](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-mysql)
    * [了解 Enterprise 註冊的保留項目使用量](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
    * [合作夥伴中心雲端解決方案提供者 (CSP) 計畫中的 Azure 保留項目](https://docs.microsoft.com/partner-center/azure-reservations)


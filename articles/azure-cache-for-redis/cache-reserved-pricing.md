---
title: 預付費計算與保留容量 - Azure 緩存為 Redis
description: 為具有預留容量的 Redis 計算資源預付 Azure 緩存
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: aded023c9f4c045f612e33d32c1e3ac71afddf02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77530298"
---
# <a name="prepay-for-azure-cache-for-redis-compute-resources-with-reserved-capacity"></a>為具有預留容量的 Redis 計算資源預付 Azure 緩存

Redis 的 Azure 緩存現在通過預付費計算資源與即用即付價格相比，説明您節省資金。 使用用於 Redis 保留容量的 Azure 緩存，您可以提前承諾在緩存上執行一到三年的週期，以獲得計算成本的顯著折扣。 要為 Redis 預留容量購買 Azure 緩存，需要指定 Azure 區域、服務層和術語。

不需要為 Redis 實例將預留分配給特定的 Azure 緩存。 已運行的 Redis 或新部署的 Azure 緩存將自動獲得保留定價的好處，最多為保留緩存大小。 透過購買保留，您會預付為期一或三年的計算費用。 購買預留後，與保留屬性匹配的 Redis Azure 緩存計算費用將不再按即用即付費率收費。 預留不包括與緩存關聯的網路或存儲費用。 在預訂期限結束時，計費權益將過期，Redis 的 Azure 緩存以即用即付價格計費。 保留不會自動更新。 有關定價資訊，請參閱[Redis 保留容量提供的 Azure 緩存。](https://azure.microsoft.com/pricing/details/cache)

您可以在[Azure 門戶](https://portal.azure.com/)中為 Redis 保留容量購買 Azure 緩存。 要購買預留容量：

* 您必須在至少一個企業或個人訂閱的擁有者角色中，使用即用即付費率。
* 針對企業訂用帳戶，必須在 [EA 入口網站](https://ea.azure.com/)中啟用**新增保留執行個體**。 或者，如果該設定已停用，則您必須是訂用帳戶上的 EA 系統管理員。
* 對於雲解決方案供應商 （CSP） 計畫，只有管理代理或銷售代理可以購買 Azure 緩存以保留容量。

有關企業客戶和即用即付客戶如何收取預訂費用的詳細資訊，請參閱[瞭解企業註冊的 Azure 預留使用方式](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)，並[瞭解即用即付訂閱的 Azure 預留使用方式](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)。


## <a name="determine-the-right-cache-size-before-purchase"></a>購買前確定正確的緩存大小

預留的大小應基於特定區域內的現有或即將部署的緩存使用的計算總量，並使用相同的服務層。

例如，假設您正在運行一個通用，Gen5 = 32 vCore 緩存，以及兩個經過優化的記憶體，Gen5 = 16 vCore 緩存。 此外，假設您計畫在下個月部署額外的通用用途，Gen5 = 32 vCore 資料庫伺服器，以及一個記憶體優化的 Gen5 = 16 vCore 資料庫伺服器。 假設您知道至少需要這些資源 1 年。 在這種情況下，您應該購買 64 （2x32） vCore，單資料庫通用 1 年預訂 - Gen5 和 48 （2x16 + 16） vCore 1 年預留，用於優化單個資料庫記憶體 - Gen5


## <a name="buy-azure-cache-for-redis-reserved-capacity"></a>為 Redis 預留容量購買 Azure 緩存

1. 登錄到 Azure[門戶](https://portal.azure.com/)。
2. 選擇**所有服務** > **預訂**。
3. 選擇 **"添加**"，然後在"購買預留"窗格中，選擇 **"Azure 緩存"以用於 Redis**以購買緩存的新預留。
4. 填寫所需的欄位。 與您選擇的屬性匹配的現有或新資料庫有資格獲得預留容量折扣。 獲得折扣的 Redis 實例的 Azure 緩存的實際數量取決於所選的範圍和數量。


![保留定價概述](media/cache-reserved-pricing/cache-reserved-price.png)


下表描述了必要欄位。

| 欄位 | 描述 |
| :------------ | :------- |
| 訂用帳戶   | 用於為 Redis 預留容量預留的 Azure 緩存付費的訂閱。 訂閱上的付款條件將收取 Redis 預留容量預留的 Azure 緩存的前期費用。 訂閱類型必須是企業協定（產品/服務編號：MS-AZR-0017P 或 MS-AZR-0148P）或具有即用即付定價的單個協定（產品/服務編號：MS-AZR-0003P 或 MS-AZR-0023P）。 針對企業訂用帳戶，費用會從註冊的承諾用量金額餘額扣除或作為超額部分收費。 對於具有即用即付定價的個人訂閱，費用將計入訂閱上的信用卡或發票付款條件。
| 影響範圍 | 保留項目範圍可以涵蓋一個訂用帳戶或多個訂用帳戶 (共用範圍)。 如果您選取： </br></br> **共用**，預留折扣將應用於在計費上下文中任何訂閱中運行的 Redis 實例的 Azure 緩存。 針對企業客戶，共用範圍是註冊，並包含註冊中的所有訂用帳戶。 針對隨用隨付客戶，共用範圍是帳戶系統管理員所建立的所有隨用隨付訂用帳戶。</br></br> **單個訂閱**，保留折扣將應用於此訂閱中 Redis 實例的 Azure 緩存。 </br></br> **單個資源組**，預留折扣應用於所選訂閱中的 Redis 實例的 Azure 緩存以及該訂閱中的選定資源組。
| 區域 | Azure 緩存為 Redis 保留容量預留所覆蓋的 Azure 區域。
| 定價層 | Redis 伺服器的 Azure 緩存的服務層。
| 詞彙 | 一年或三年
| 數量 | 在 Azure 緩存中為 Redis 預留容量預留而購買的計算資源量。 數量是所選 Azure 區域和服務層中保留的多個緩存，您將獲得計費折扣。 例如，如果您正在運行或計畫為美國東部區域的總緩存容量為 26 GB 的 Redis 伺服器運行 Azure 緩存，則將數量指定為 26，以最大化所有緩存的好處。

## <a name="cancel-exchange-or-refund-reservations"></a>取消、交換保留或進行退費

您可以取消、交換保留或進行退費，但有某些限制。 如需詳細資訊，請參閱 [Azure 保留的自助式交換和退費](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund)。

## <a name="cache-size-flexibility"></a>緩存大小靈活性

緩存大小靈活性可説明您在服務層和地區內向上或向下擴展，而不會丟失預留容量權益。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡

如果您有疑問或需要幫助，[請創建支援請求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>後續步驟

保留折扣將自動應用於與預留範圍和屬性匹配的 Redis 實例的 Azure 緩存。 您可以透過 Azure 入口網站、PowerShell、Azure CLI 或 API 來更新保留容量的範圍。

*  要瞭解如何將預留容量折扣應用於 Redis 的 Azure 緩存，請參閱[瞭解 Azure 預留折扣](../cost-management-billing/reservations/understand-azure-cache-for-redis-reservation-charges.md)

* 若要深入了解 Azure 保留項目，請參閱下列文章：

    * [什麼是 Azure 保留項目？](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
    * [管理 Azure 預留](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
    * [了解 Azure 保留折扣](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
    * [了解隨用隨付訂用帳戶的保留使用量](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-mysql)
    * [了解 Enterprise 註冊的保留項目使用量](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
    * [合作夥伴中心雲端解決方案提供者 (CSP) 計畫中的 Azure 保留項目](https://docs.microsoft.com/partner-center/azure-reservations)


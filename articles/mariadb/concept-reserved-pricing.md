---
title: 預付費計算與保留容量 - Azure 資料庫為 MariaDB
description: 為具有預留容量的 MariaDB 計算資源的 Azure 資料庫預付費用
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 9a6bd3cab41c69075f5170a8a3aad4f059d970e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159042"
---
# <a name="prepay-for-azure-database-for-mariadb-compute-resources-with-reserved-capacity"></a>為具有預留容量的 MariaDB 計算資源的 Azure 資料庫預付費用

MariaDB 的 Azure 資料庫現在通過預付費計算資源來説明您節省資金，而即用即付價格則有助於您節省資金。 使用用於 MariaDB 保留容量的 Azure 資料庫，您可以在 MariaDB 伺服器上提前承諾一到三年，以獲得計算成本的顯著折扣。 要為 MariaDB 保留容量購買 Azure 資料庫，需要指定 Azure 區域、部署類型、性能層和術語。 </br>

您無需為 MariaDB 伺服器將預留分配給特定的 Azure 資料庫。 已運行的 MariaDB 或新部署的 Azure 資料庫將自動獲得保留定價的好處。 透過購買保留，您會預付為期一或三年的計算費用。 購買預留後，與預留屬性匹配的 MariaDB 計算費用的 Azure 資料庫將不再按即用即付費率收費。 預留不包括與 MariaDB 資料庫伺服器關聯的軟體、網路或存儲費用。 在預訂期限結束時，計費權益將過期，MariaDB 的 Azure 資料庫以即用即付價格計費。 保留不會自動更新。 有關定價資訊，請參閱[MariaDB 保留容量產品的 Azure 資料庫。](https://azure.microsoft.com/pricing/details/mariadb/) </br>

您可以在[Azure 門戶](https://portal.azure.com/)中為 MariaDB 保留容量購買 Azure 資料庫。 要購買預留容量：

* 您必須在至少一個企業或個人訂閱的擁有者角色中，使用即用即付費率。
* 針對企業訂用帳戶，必須在 [EA 入口網站](https://ea.azure.com/)中啟用**新增保留執行個體**。 或者，如果該設定已停用，則您必須是訂用帳戶上的 EA 系統管理員。
* 對於雲解決方案供應商 （CSP） 計畫，只有管理員代理或銷售代理可以購買 Azure 資料庫，以便為 MariaDB 保留容量。 </br>

有關企業客戶和即用即付客戶如何收取預訂費用的詳細資訊，請參閱[瞭解企業註冊的 Azure 預留使用方式](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)[，並瞭解即用即付訂閱的 Azure 預留使用方式](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)。


## <a name="determine-the-right-server-size-before-purchase"></a>購買前確定正確的伺服器大小

預留的大小應基於特定區域內現有或即將部署的資料庫實例使用的計算總量，並使用相同的性能層和硬體生成。</br>

例如，假設您正在運行一個通用資料庫，Gen5 = 32 vCore MariaDB 資料庫，以及兩個經過優化的記憶體，Gen5 = 16 vCore MariaDB 資料庫。 此外，假設您計畫在下個月部署額外的通用用途，Gen5 = 32 vCore 資料庫伺服器，以及一個記憶體優化的 Gen5 = 16 vCore 資料庫伺服器。 假設您知道至少需要這些資源 1 年。 在這種情況下，您應該購買 64 （2x32） vCore，單資料庫通用 1 年預訂 - Gen5 和 48 （2x16 + 16） vCore 1 年預留，用於優化單個資料庫記憶體 - Gen5


## <a name="buy-azure-database-for-mariadb-reserved-capacity"></a>為 MariaDB 預留容量購買 Azure 資料庫

1. 登錄到 Azure[門戶](https://portal.azure.com/)。
2. 選擇**所有服務** > **預訂**。
3.  選擇 **"添加**"，然後在"購買預留"窗格中，選擇**MariaDB 的 Azure 資料庫**，以便為 MariaDB 資料庫購買新的預留。
4.  填寫所需的欄位。 與您選擇的屬性匹配的現有或新資料庫有資格獲得預留容量折扣。 獲得折扣的 MariaDB 伺服器的 Azure 資料庫的實際數量取決於所選的範圍和數量。


![保留定價概述](media/concepts-reserved-pricing/mariadb-reserved-price.png)


下表描述了必要欄位。

| 欄位 | 描述 |
| :------------ | :------- |
| 訂用帳戶   | 用於為 MariaDB 預留容量預留的 Azure 資料庫付費的訂閱。 訂閱上的付款條件將收取 MariaDB 預留容量預留的 Azure 資料庫的前期費用。 訂閱類型必須是企業協定（產品/服務編號：MS-AZR-0017P 或 MS-AZR-0148P）或具有即用即付定價的單個協定（產品/服務編號：MS-AZR-0003P 或 MS-AZR-0023P）。 針對企業訂用帳戶，費用會從註冊的承諾用量金額餘額扣除或作為超額部分收費。 對於具有即用即付定價的個人訂閱，費用將計入訂閱上的信用卡或發票付款條件。
| 影響範圍 | vCore 預留的範圍可以涵蓋一個訂閱或多個訂閱（共用範圍）。 如果您選取： </br></br> **共用**，vCore 預留折扣將應用於 Azure 資料庫，用於在計費上下文中任何訂閱中運行的 MariaDB 伺服器。 針對企業客戶，共用範圍是註冊，並包含註冊中的所有訂用帳戶。 針對隨用隨付客戶，共用範圍是帳戶系統管理員所建立的所有隨用隨付訂用帳戶。</br></br> **單一訂閱**，vCore 預留折扣將應用於此訂閱中的 MariaDB 伺服器的 Azure 資料庫。 </br></br> **單個資源組**，預留折扣應用於所選訂閱中的 MariaDB 伺服器的 Azure 資料庫以及該訂閱中的選定資源組。
| 區域 | Azure 資料庫為 MariaDB 保留容量預留所覆蓋的 Azure 區域。
| 部署類型 | 要為其購買預留的 MariaDB 資源類型的 Azure 資料庫。
| 效能層級 | MariaDB 伺服器 Azure 資料庫的服務層。
| 詞彙 | 一年
| 數量 | 在 Azure 資料庫中為 MariaDB 預留容量預留而購買的計算資源量。 數量是所選 Azure 區域和"性能"層中保留的 vCore 的多個 vCore，它們將獲得計費折扣。 例如，如果您正在運行或計畫為 MariaDB 伺服器運行 Azure 資料庫，其總計算容量為美國東部區域的 Gen5 16 vCores，則將數量指定為 16，以最大化所有伺服器的好處。

## <a name="cancel-exchange-or-refund-reservations"></a>取消、交換保留或進行退費

您可以取消、交換保留或進行退費，但有某些限制。 如需詳細資訊，請參閱 [Azure 保留的自助式交換和退費](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund)。

## <a name="vcore-size-flexibility"></a>vCore 大小彈性

vCore 大小彈性可協助您在效能層級和區域內相應增加或相應減少，而不會失去保留容量優勢。 

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡

如果您有疑問或需要幫助，[請創建支援請求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>後續步驟

vCore 預留折扣將自動應用於與 MariaDB 預留容量預留範圍和屬性的 Azure 資料庫匹配的 MariaDB 伺服器的 Azure 資料庫數。 您可以通過 Azure 門戶、PowerShell、CLI 或 API 更新 MariaDB 保留容量預留的 Azure 資料庫的範圍。 </br></br>
要瞭解如何管理 MariaDB 保留容量的 Azure 資料庫，請參閱管理 MariaDB 保留容量的 Azure 資料庫。

若要深入了解 Azure 保留項目，請參閱下列文章：

* [什麼是 Azure 預留](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)？
* [管理 Azure 預留](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
* [了解 Azure 保留折扣](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
* [了解隨用隨付訂用帳戶的保留使用量](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-mariadb)
* [了解 Enterprise 註冊的保留項目使用量](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
* [合作夥伴中心雲端解決方案提供者 (CSP) 計畫中的 Azure 保留項目](https://docs.microsoft.com/partner-center/azure-reservations)

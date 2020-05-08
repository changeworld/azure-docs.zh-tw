---
title: 預付具有保留容量的計算-適用於 MariaDB 的 Azure 資料庫
description: 預付以保留容量適用於 MariaDB 的 Azure 資料庫計算資源
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 47ddad70b4764fedefb50b93de2b7f078cf9fd27
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/03/2020
ms.locfileid: "82732898"
---
# <a name="prepay-for-azure-database-for-mariadb-compute-resources-with-reserved-capacity"></a>預付以保留容量適用於 MariaDB 的 Azure 資料庫計算資源

適用於 MariaDB 的 Azure 資料庫現在可協助您節省與隨用隨付價格相比的計算資源還要划算成本。 有了適用於 MariaDB 的 Azure 資料庫保留容量，您可以在適用于 mariadb 伺服器上預先承諾一年或三年的時間，以取得計算成本的可觀折扣。 若要購買適用於 MariaDB 的 Azure 資料庫保留容量，您必須指定 Azure 區域、部署類型、效能層級和期限。 </br>

您不需要將保留指派給特定的適用於 MariaDB 的 Azure 資料庫伺服器。 已執行適用於 MariaDB 的 Azure 資料庫或新部署的，將會自動獲得保留定價的權益。 透過購買保留，您會預付為期一或三年的計算費用。 一旦您購買保留，符合保留屬性的 Azure database for 適用于 mariadb 計算費用將不再以隨用隨付費率計費。 保留專案並不涵蓋與適用于 mariadb 資料庫伺服器相關聯的軟體、網路或儲存體費用。 在保留期限結束時，計費權益會到期，而適用於 MariaDB 的 Azure 資料庫會以隨用隨付價格計費。 保留不會自動更新。 如需定價資訊，請參閱[適用於 MariaDB 的 Azure 資料庫保留容量](https://azure.microsoft.com/pricing/details/mariadb/)供應專案。 </br>

您可以在[Azure 入口網站](https://portal.azure.com/)中購買適用於 MariaDB 的 Azure 資料庫保留容量。 保留的付款方式可為[預先付款或每月付款](../cost-management-billing/reservations/monthly-payments-reservations.md)。 若要購買保留容量：

* 您至少必須是一個企業或個別訂用帳戶的擁有者角色，且具有隨用隨付費率。
* 針對企業訂用帳戶，必須在 [EA 入口網站](https://ea.azure.com/)中啟用**新增保留執行個體**。 或者，如果該設定已停用，則您必須是訂用帳戶上的 EA 系統管理員。
* 針對雲端解決方案提供者（CSP）計畫，只有系統管理員代理程式或銷售代理人可以適用於 MariaDB 的 Azure 資料庫保留容量購買。 </br>

有關企業客戶和預付型方案客戶如何針對保留購買收費的詳細資訊，請參閱[瞭解 enterprise 註冊的 azure 保留使用量](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)和[瞭解隨用隨付訂用帳戶的 azure 保留使用量](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)。


## <a name="determine-the-right-server-size-before-purchase"></a>在購買前判斷正確的伺服器大小

保留大小應根據特定區域內現有或即將部署的資料庫實例所使用的總計算量，並使用相同的效能層級和硬體世代。</br>

例如，假設您執行一個一般用途第5代– 32 vCore 適用于 mariadb 資料庫，以及兩個記憶體優化，第5代– 16 vCore 適用于 mariadb 資料庫。 此外，假設您計畫在下個月部署其他一般用途第5代– 32 vCore 資料庫伺服器，以及一個記憶體優化、第5代– 16 vCore 資料庫伺服器。 假設您知道您至少需要一年的這些資源。 在此情況下，您應該購買64（2x32）虛擬核心，單一資料庫一般目的-第5代和48（2x16 + 16） vCore 1 年保留，適用于單一資料庫記憶體優化-第5代


## <a name="buy-azure-database-for-mariadb-reserved-capacity"></a>購買適用於 MariaDB 的 Azure 資料庫保留容量

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 選取 [**所有服務** > ] [**保留**]。
3.  選取 [**新增**]，然後在 [購買保留] 窗格中選取 [**適用於 MariaDB 的 Azure 資料庫**]，為您的適用于 mariadb 資料庫購買新的保留。
4.  填寫必要欄位。 符合您所選屬性的現有或新資料庫，可以取得保留容量折扣。 取得折扣的適用於 MariaDB 的 Azure 資料庫伺服器實際數目，取決於選取的範圍和數量。


![保留定價的總覽](media/concepts-reserved-pricing/mariadb-reserved-price.png)


下表描述必要的欄位。

| 欄位 | 描述 |
| :------------ | :------- |
| 訂用帳戶   | 用來支付保留容量適用於 MariaDB 的 Azure 資料庫的訂用帳戶。 訂用帳戶的付款方法會收取適用於 MariaDB 的 Azure 資料庫保留容量保留期的前期成本。 訂用帳戶類型必須是 enterprise 合約（供應專案號碼： MS-AZR-0017P-Ms-azr-0017p 或 MS-MS-AZR-0017P-Ms-azr-0148p）或具有隨用隨付定價的個別合約（供應專案號碼： MS-MS-AZR-0017P-Ms-azr-0003p 或 MS-MS-AZR-0017P-Ms-azr-0023p）。 針對企業訂用帳戶，費用會從註冊的承諾用量金額餘額扣除或作為超額部分收費。 針對使用隨用隨付定價的個別訂用帳戶，費用會依訂用帳戶的信用卡或發票付款方法計費。
| 影響範圍 | VCore 保留的範圍可以涵蓋一個訂用帳戶或多個訂閱（共用範圍）。 如果您選取： </br></br> **共用**的 vCore 保留折扣會套用至計費內容內任何訂用帳戶中執行的適用於 MariaDB 的 Azure 資料庫伺服器。 針對企業客戶，共用範圍是註冊，並包含註冊中的所有訂用帳戶。 針對隨用隨付客戶，共用範圍是帳戶系統管理員所建立的所有隨用隨付訂用帳戶。</br></br> **單一訂**用帳戶，vCore 保留折扣會套用到此訂用帳戶中適用於 MariaDB 的 Azure 資料庫伺服器。 </br></br> **單一資源群組**，保留折扣會套用至所選訂用帳戶中的適用於 MariaDB 的 Azure 資料庫伺服器，以及該訂用帳戶內選取的資源群組。
| 區域 | 適用於 MariaDB 的 Azure 資料庫保留容量保留所涵蓋的 Azure 區域。
| 部署類型 | 您想要為其購買保留的適用於 MariaDB 的 Azure 資料庫資源類型。
| 效能層級 | 適用於 MariaDB 的 Azure 資料庫伺服器的服務層級。
| 詞彙 | 一年
| 數量 | 在適用於 MariaDB 的 Azure 資料庫保留容量保留內購買的計算資源量。 數量是所選 Azure 區域中的數個虛擬核心，以及要保留的效能層級，將會獲得計費折扣。 例如，如果您正在適用於 MariaDB 的 Azure 資料庫執行或計畫在美國東部區域的計算容量總計為第5代16虛擬核心，則您會將數量指定為16，以將所有伺服器的權益最大化。

## <a name="cancel-exchange-or-refund-reservations"></a>取消、交換保留或進行退費

您可以取消、交換保留或進行退費，但有某些限制。 如需詳細資訊，請參閱[Azure 保留的自助交換和退款](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund)。

## <a name="vcore-size-flexibility"></a>vCore 大小彈性

vCore 大小彈性可協助您在效能層級和區域內相應增加或相應減少，而不會失去保留容量優勢。 

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡

如果您有問題或需要協助，請[建立支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>後續步驟

VCore 保留折扣會自動套用到符合適用於 MariaDB 的 Azure 資料庫保留容量保留範圍和屬性的適用於 MariaDB 的 Azure 資料庫伺服器數目。 您可以透過 Azure 入口網站、PowerShell、CLI 或 API 來更新 Azure 資料庫的範圍，以進行適用于 mariadb 保留容量保留。 </br></br>
若要瞭解如何管理適用於 MariaDB 的 Azure 資料庫保留容量，請參閱管理適用於 MariaDB 的 Azure 資料庫保留容量。

若要深入了解 Azure 保留項目，請參閱下列文章：

* [什麼是 Azure 保留](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)專案？
* [管理 Azure 保留項目](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
* [了解 Azure 保留折扣](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
* [了解隨用隨付訂用帳戶的保留使用量](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-mariadb)
* [了解 Enterprise 註冊的保留項目使用量](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
* [合作夥伴中心雲端解決方案提供者 (CSP) 計畫中的 Azure 保留項目](https://docs.microsoft.com/partner-center/azure-reservations)

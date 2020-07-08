---
title: 保留的計算定價-適用於 PostgreSQL 的 Azure 資料庫超大規模資料庫（Citus）
description: 預付具有保留容量的適用於 PostgreSQL 的 Azure 資料庫超大規模資料庫（Citus）計算資源
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: dcf38a3bcd41ba41bb7cf3b16d022a7a45734c4a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85217883"
---
# <a name="prepay-for-azure-database-for-postgresql---hyperscale-citus-compute-resources-with-reserved-capacity"></a>預付具有保留容量的適用於 PostgreSQL 的 Azure 資料庫超大規模資料庫（Citus）計算資源

適用於 PostgreSQL 的 Azure 資料庫–超大規模資料庫（Citus）現在可協助您節省與隨用隨付價格相比的計算資源還要划算費用。 有了超大規模資料庫（Citus）保留容量，您可以在超大規模資料庫（Citus）伺服器群組的一年或三年期前預付承諾用量，以取得計算成本的可觀折扣。 若要購買超大規模資料庫（Citus）保留容量，您必須指定 Azure 區域、保留期限和計費頻率。

> [!IMPORTANT]
> 此頁面是關於適用於 PostgreSQL 的 Azure 資料庫的保留容量–超大規模資料庫（Citus）。 如需適用於 PostgreSQL 的 Azure 資料庫–單一伺服器的保留容量相關資訊，請參閱[此頁面](/azure/postgresql/concept-reserved-pricing)。

您不需要將保留指派給特定的超大規模資料庫（Citus）伺服器群組。 已在執行中的超大規模資料庫（Citus）伺服器群組或新部署的伺服器群組，將會自動獲得保留定價的權益。 藉由購買保留，您就可以預先支付一或三年期的計算成本。 一旦您購買保留，符合保留屬性的超大規模資料庫（Citus）計算費用就不再以隨用隨付費率計費。 保留專案並不涵蓋與超大規模資料庫（Citus）伺服器群組相關聯的軟體、網路或儲存體費用。 在保留期限結束時，計費權益會到期，而超大規模資料庫（Citus）伺服器群組則會以隨用隨付價格計費。 保留專案不會 autorenew。 如需定價資訊，請參閱[適用於 PostgreSQL 的 Azure 資料庫–超大規模資料庫（Citus）保留容量](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/)供應專案。

您可以在[Azure 入口網站](https://portal.azure.com/)中購買超大規模資料庫（Citus）保留容量。 保留的付款方式可為[預先付款或每月付款](https://docs.microsoft.com/azure/cost-management-billing/reservations/monthly-payments-reservations)。 若要購買保留容量：

* 您至少必須是一個企業或個別訂用帳戶的擁有者角色，且具有隨用隨付費率。
* 針對企業訂用帳戶，必須在 [EA 入口網站](https://ea.azure.com/)中啟用**新增保留執行個體**。 或者，如果該設定已停用，則您必須是訂用帳戶上的 EA 系統管理員。
* 針對雲端解決方案提供者（CSP）計畫，只有系統管理員代理程式或銷售代理人可以購買超大規模資料庫（Citus）保留容量。

如需企業客戶和預付型方案客戶如何支付保留購買費用的詳細資訊，請參閱[瞭解 enterprise 註冊的 azure 保留使用量](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)和[瞭解隨用隨付訂用帳戶的 azure 保留使用量](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)。

## <a name="determine-the-right-server-group-size-before-purchase"></a>在購買前判斷正確的伺服器群組大小

保留大小應根據現有或即將部署的協調器，以及特定區域內超大規模資料庫（Citus）伺服器群組中的背景工作角色節點所使用的總計算量。

例如，假設您正在執行一個超大規模資料庫（Citus）伺服器群組，其中包含16個 vCore 協調器和三個8個 vCore 背景工作節點。 此外，我們假設您計畫在下個月內部署額外的超大規模資料庫（Citus）伺服器群組，其中包含 32 vCore 協調員和兩個 4 vCore 背景工作節點。 假設您至少會需要一年的這些資源。

在此情況下，您應該購買一年的保留期

* 協調器節點的總計16虛擬核心 + 32 虛擬核心 = 48 虛擬核心
* 總共三個節點 x 8 虛擬核心 + 2 個節點 x 4 虛擬核心 = 24 + 8 = 32 虛擬核心用於背景工作節點

## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>購買適用於 PostgreSQL 的 Azure 資料庫保留容量

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 選取 [所有服務] > [保留]。
3. 選取 [**新增**]，然後在 [購買保留] 窗格中選取 [**適用於 PostgreSQL 的 Azure 資料庫**]，為您的于 postgresql 資料庫購買新的保留。
4. 選取 [超大規模資料庫（Citus）要購買的計算類型]，然後按一下 [**選取**] 按鈕。
5. 在 [**產品**] 索引標籤上，檢查所選計算類型的數量。
4. 繼續進行 [**購買 + 審核**] 索引標籤以完成購買。

下表描述必要的欄位。

| 欄位        | 描述                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 訂用帳戶 | 用來支付保留容量適用於 PostgreSQL 的 Azure 資料庫的訂用帳戶。 訂用帳戶的付款方法會收取適用於 PostgreSQL 的 Azure 資料庫保留容量保留期的前期成本。 訂用帳戶類型必須是 enterprise 合約（供應專案號碼： MS-AZR-0017P-Ms-azr-0017p 或 MS-MS-AZR-0017P-Ms-azr-0148p）或具有隨用隨付定價的個別合約（供應專案號碼： MS-MS-AZR-0017P-Ms-azr-0003p 或 MS-MS-AZR-0017P-Ms-azr-0023p）。 若為企業訂用帳戶，費用會從註冊的承諾用量金額餘額扣除，或以超額部分收費。 針對使用隨用隨付定價的個別訂用帳戶，費用會依訂用帳戶的信用卡或發票付款方法計費。                                                                                  |
| 影響範圍        | 虛擬核心保留容量範圍可以涵蓋一個訂用帳戶或多個訂用帳戶 (共用範圍)。 如果您選取： <br><br> **共用**的 vCore 保留折扣會套用至計費內容內任何訂用帳戶中執行的超大規模資料庫（Citus）伺服器群組。 針對企業客戶，共用範圍是註冊，並包含註冊中的所有訂用帳戶。  針對隨用隨付客戶，共用範圍是帳戶系統管理員所建立的所有隨用隨付訂用帳戶。 <br><br> **單一訂**用帳戶，vCore 保留折扣會套用至此訂用帳戶中的超大規模資料庫（Citus）伺服器群組。 <br><br> **單一資源群組，** 保留折扣會套用至所選訂用帳戶中的超大規模資料庫（Citus）伺服器群組，以及該訂用帳戶內選取的資源群組。 |
| 區域       | 由適用於 PostgreSQL 的 Azure 資料庫–超大規模資料庫（Citus）保留容量保留所涵蓋的 Azure 區域。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| 詞彙         | 一年或三年。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| 數量     | 在超大規模資料庫（Citus）保留容量中所購買的計算資源量。 具體而言，在所選 Azure 區域中虛擬核心的協調器或背景工作角色節點數目會被保留，而這會獲得計費折扣。 例如，如果您在美國東部區域中執行（或打算執行）超大規模資料庫（Citus）伺服器群組，且計算容量總計為64協調員節點虛擬核心和 32 worker 節點虛擬核心，則您會分別為協調器和背景工作節點指定數量為64和32，以最大化所有伺服器的效益。                                                                                                                                                                                                                                                     |



## <a name="cancel-exchange-or-refund-reservations"></a>取消、交換保留或進行退費

您可以取消、交換保留或進行退費，但有某些限制。 如需詳細資訊，請參閱 [Azure 保留的自助式交換和退費](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund)。

## <a name="vcore-size-flexibility"></a>vCore 大小彈性

vCore 大小彈性可協助您相應增加或減少區域內的協調器和背景工作節點，而不會失去保留容量權益。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡

如果您有問題或需要協助，請[建立支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>後續步驟

VCore 保留折扣會自動套用至符合適用於 PostgreSQL 的 Azure 資料庫保留容量保留範圍和屬性的超大規模資料庫（Citus）伺服器群組數目。 您可以透過 Azure 入口網站、PowerShell、CLI 或 API，更新 Azure database for 于 postgresql –超大規模資料庫（Citus）保留容量保留的範圍。

若要深入了解 Azure 保留項目，請參閱下列文章：

* [什麼是 Azure 保留項目？](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
* [管理 Azure 保留項目](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
* [了解 Azure 保留折扣](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
* [了解隨用隨付訂用帳戶的保留使用量](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-postgresql)
* [了解 Enterprise 註冊的保留項目使用量](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
* [合作夥伴中心雲端解決方案提供者 (CSP) 計畫中的 Azure 保留項目](https://docs.microsoft.com/partner-center/azure-reservations)

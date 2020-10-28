---
title: 使用保留容量來節省計算成本
titleSuffix: Azure SQL Database & SQL Managed Instance
description: 瞭解如何購買 Azure SQL Database 和 SQL 受控執行個體保留容量，以節省您的計算成本。
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein
ms.date: 10/13/2020
ms.openlocfilehash: b4a8b809fc135a4af26fae10912890f2017153f4
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92780915"
---
# <a name="save-costs-for-resources-with-reserved-capacity---azure-sql-database--sql-managed-instance"></a>使用保留容量來節省資源成本-Azure SQL Database & SQL 受控執行個體
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)] 

藉由承諾與隨用隨付價格相較的計算資源保留，來節省 Azure SQL Database 和 SQL 受控執行個體的成本。 使用保留容量時，您可以在一或三年期內 SQL Database 及/或 SQL 受控執行個體使用，以取得計算成本的大量折扣。 若要購買保留容量，您需要指定 Azure 區域、部署類型、效能層級和期限。

您不需要將保留指派給特定的資料庫或受控實例。 比對已在執行中的現有部署，或新部署的現有部署會自動取得權益。 藉由購買保留，您可以在一或三年的期間內認可計算成本的使用量。 當您購買保留專案時，符合保留屬性的計算費用就不會再以隨用隨付費率計費。 

保留適用于主要和可計費次要計算複本，但不涵蓋與服務相關聯的軟體、網路或儲存體費用。 在保留期限結束時，帳單權益會過期，而資料庫或受控實例會以隨用隨付價格計費。 保留不會自動續約。 如需定價資訊，請參閱 [保留容量](https://azure.microsoft.com/pricing/details/sql-database/managed/)供應專案。

您可以在 [Azure 入口網站](https://portal.azure.com)中購買保留容量。 保留的付款方式可為[預先付款或每月付款](../../cost-management-billing/reservations/prepare-buy-reservation.md)。 若要購買保留容量：

- 您必須是至少一個具有隨用隨付費率的企業或個別訂用帳戶的擁有者角色。
- 針對企業訂用帳戶，必須在 [EA 入口網站](https://ea.azure.com)中啟用 **新增保留執行個體** 。 或者，如果該設定已停用，則您必須是訂用帳戶上的 EA 系統管理員。 保留容量。

如需企業客戶和隨用隨付客戶如何針對保留購買收費的詳細資訊，請參閱 [瞭解您企業註冊的 azure 保留使用量](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) ，以及 [瞭解隨用隨付訂用帳戶的 azure 保留使用量](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)。

> [!NOTE]
> 購買保留容量不會預先配置或保留特定基礎結構資源 (虛擬機器或節點) 供您使用。

## <a name="determine-correct-size-before-purchase"></a>在購買前判斷正確的大小

保留大小應該以現有或即將部署的資料庫或特定區域內受管理的實例所使用的總計算量為基礎，並使用相同的效能層級和硬體世代。

例如，假設您執行的是一個一般用途第5代–16個 vCore 彈性集區，以及兩個商務關鍵性第5代– 4 vCore 單一資料庫。 此外，假設您打算在下個月內部署額外的一般用途第5代– 16 vCore 彈性集區和一個商務關鍵第5代– 32 vCore 彈性集區。 還有，假設您知道您需要使用這些資源至少 1 年的時間。 在此情況下，您應該為單一資料庫/彈性集區購買 32 (2x16) 虛擬核心1年的保留，以及針對單一資料庫/彈性集區商務關鍵第5代) vCore 1 年保留的 40 (2x4 + 32 1 年的保留。

## <a name="buy-reserved-capacity"></a>購買保留容量

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [所有服務] > [保留]。
3. 選取 [ **新增** ]，然後在 [ **購買保留** ] 窗格中，選取 [ **SQL Database** 購買 SQL Database 的新保留。
4. 填寫必要欄位。 SQL Database 和 SQL 受控執行個體中符合您所選屬性的現有資料庫，都有資格獲得保留容量折扣。 取得折扣的資料庫或受控實例的實際數目取決於選取的範圍和數量。

    ![提交保留容量購買之前的螢幕擷取畫面](./media/reserved-capacity-overview/sql-reserved-vcores-purchase.png)

    下表說明必要的欄位。
    
    | 欄位      | 描述|
    |------------|--------------|
    |訂用帳戶|用來支付容量保留的訂用帳戶。 保留項目的預付費用會透過訂用帳戶的付款方式收取。 訂用帳戶類型必須是 enterprise 合約 (供應專案號碼 MS-AZR-0003P->ms-azr-0017p 或 MS-AZR-0003P-Ms-azr-0148p) 或具有隨用隨付定價的個別合約 (供應專案號碼： MS-MS-AZR-0003P-Ms-azr-0003p 或 MS-MS-AZR-0003P-Ms-azr-0023p) 。 針對企業訂用帳戶，費用會從註冊的承諾用量金額餘額扣除或作為超額部分收費。 針對具有隨用隨付定價的個別訂用帳戶，費用會以訂用帳戶的信用卡或發票付款方法計費。|
    |影響範圍       |VCore 保留的範圍可以涵蓋一個訂用帳戶或多個訂用帳戶 (共用範圍) 。 如果您選取 <br/><br/>**共用** ： vCore 保留折扣會套用至在帳單內容內的任何訂用帳戶中執行的資料庫或受控實例。 針對企業客戶，共用範圍是註冊，並包含註冊中的所有訂用帳戶。 針對隨用隨付客戶，共用範圍是帳戶系統管理員所建立的所有隨用隨付訂用帳戶。<br/><br/>**單一訂** 用帳戶，vCore 保留折扣會套用到此訂用帳戶中的資料庫或受控實例。 <br/><br/>**單一資源群組** ，保留折扣會套用至所選訂用帳戶中的資料庫實例或受控實例，以及該訂用帳戶內選取的資源群組。|
    |區域      |容量保留所涵蓋的 Azure 區域。|
    |部署類型|您要為其購買保留容量的 SQL 資源類型。|
    |效能層級|資料庫或受控實例的服務層級。 |
    |詞彙        |一年或三年。|
    |數量    |容量保留內所購買的計算資源數量。 數量是所選 Azure 區域和效能層級中所保留的虛擬核心數，將會取得帳單折扣。 例如，如果您執行或計畫在美國東部區域中執行多個資料庫，且其計算容量總計為第5代16虛擬核心，則您會將數量指定為16，以最大化所有資料庫的權益。 |

1. 在 [ **成本** ] 區段中，檢查容量保留的成本。
1. 選取 [購買]。
1. 選取 [檢視此保留]  以查看您的購買狀態。

## <a name="cancel-exchange-or-refund-reservations"></a>取消、交換保留或進行退費

您可以取消、交換保留或進行退費，但有某些限制。 如需詳細資訊，請參閱 [Azure 保留的自助式交換和退費](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)。

## <a name="vcore-size-flexibility"></a>vCore 大小彈性

vCore 大小彈性可協助您在效能層級和區域內相應增加或相應減少，而不會失去保留容量優勢。 保留容量也可讓您彈性地將經常性資料庫移入和移出彈性集區， (在相同區域和效能層級內的彈性集區，) 作為一般作業的一部分，而不會失去保留容量的優點。 藉由在保留中保留未套用的緩衝區，您可以有效地管理效能尖峰，而不會超過預算。

## <a name="limitation"></a>限制

您無法在 SQL Database 中保留以 DTU 為基礎的 (basic、standard 或 premium) 資料庫。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡

如果您有問題或需要協助，請[建立支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>後續步驟

VCore 保留折扣會自動套用至符合容量保留範圍和屬性的資料庫或受控實例數目。 您可以透過 [Azure 入口網站](https://portal.azure.com)、PowerShell、AZURE CLI 或 API 來更新容量保留的範圍。

若要瞭解如何管理容量保留，請參閱 [管理保留容量](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)。

若要深入了解 Azure 保留項目，請參閱下列文章：

- [什麼是 Azure 保留項目？](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [管理 Azure 保留項目](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [了解 Azure 保留折扣](../../cost-management-billing/reservations/understand-reservation-charges.md)
- [了解隨用隨付訂用帳戶的保留使用量](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [了解 Enterprise 註冊的保留項目使用量](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [合作夥伴中心雲端解決方案提供者 (CSP) 計畫中的 Azure 保留項目](/partner-center/azure-reservations)
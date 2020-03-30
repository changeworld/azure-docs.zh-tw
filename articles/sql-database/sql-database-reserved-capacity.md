---
title: 節省成本
description: 了解如何購買 Azure SQL Database 保留容量，以節省計算費用。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
ms.date: 08/29/2019
ms.openlocfilehash: 0713e1ed98cc87b95cad1f84672148cd062e0b58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979978"
---
# <a name="save-costs-for-sql-database-compute-resources-with-azure-sql-database-reserved-capacity"></a>使用 Azure SQL 資料庫預留容量節省 SQL 資料庫計算資源的成本

與即用即付價格相比，通過訂閱計算資源預留，使用 Azure SQL 資料庫節省資金。 使用 Azure SQL 資料庫預留容量時，您可以承諾在一到三年內使用 SQL 資料庫，以便顯著降低計算成本。 若要購買 SQL Database 保留容量，您必須指定 Azure 區域、部署類型、效能層級與期間。


您不需將保留指派給特定的 SQL Database 執行個體 (單一資料庫、彈性集區或受控執行個體)。 只要符合已執行或新部署的 SQL Database 執行個體，就會自動獲得好處。 通過購買預訂，您將承諾使用一年或三年的計算成本。 購買保留容量後，符合保留屬性的 SQL Database 計算費用就不會再按照預付型方案的費率來收費。 保留容量並未涵蓋與 SQL Database 執行個體相關聯的軟體、網路或儲存體費用。 在保留期限結束時，計費好處會到期，隨後 SQL Database 便會按照預付型方案的費率來計費。 保留不會自動更新。 如需定價資訊，請參閱 [SQL Database 保留容量供應項目](https://azure.microsoft.com/pricing/details/sql-database/managed/)。

您可以在 [Azure 入口網站](https://portal.azure.com)購買 Azure SQL Database 保留容量。 保留的付款方式可為[預先付款或每月付款](../cost-management-billing/reservations/monthly-payments-reservations.md)。 若要購買 SQL Database 保留容量：

- 您必須在至少一個企業或個人訂閱的擁有者角色中，使用即用即付費率。
- 針對企業訂用帳戶，必須在 [EA 入口網站](https://ea.azure.com)中啟用**新增保留執行個體**。 或者，如果該設定已停用，則您必須是訂用帳戶上的 EA 系統管理員。
- 若為雲端解決方案提供者 (CSP) 方案，則只有系統管理員代理人或銷售人員可以購買 SQL Database 保留容量。

有關企業客戶和即用即付客戶如何收取預訂費用的詳細資訊，請參閱[瞭解企業註冊的 Azure 預留使用方式](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)[，並瞭解即用即付訂閱的 Azure 預留使用方式](../cost-management-billing/reservations/understand-reserved-instance-usage.md)。

## <a name="determine-the-right-sql-size-before-purchase"></a>先決定正確的 SQL 大小再購買

保留的大小應根據特定區域內現有或即將部署的單一資料庫、彈性集區或受控執行個體所使用的總計算量，並使用相同效能層級和硬體世代來決定。

例如，假設您要執行一個一般用途 Gen5 – 16 個虛擬核心的彈性集區，以及兩個商務關鍵性 Gen5 – 4 個虛擬核心的單一資料庫。 此外，假設您打算在下個月再部署一個一般用途 Gen5 – 16 個虛擬核心的彈性集區，以及一個商務關鍵性 Gen5 – 32 個虛擬核心的彈性集區。 還有，假設您知道您需要使用這些資源至少 1 年的時間。 在此情況下，您應該針對單一/彈性集區一般用途 - Gen5 購買 32 (2x16) 虛擬核心、為期 1 年的保留，以及針對單一/彈性集區商務關鍵性 - Gen5 購買 40 (2x4 + 32) 虛擬核心、為期 1 年的保留。

## <a name="buy-sql-database-reserved-capacity"></a>購買 SQL Database 保留容量

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 選擇**所有服務** > **預訂**。
3. 選擇 **"添加**"，然後在"購買預留"窗格中選擇**SQL 資料庫**以購買 SQL 資料庫的新預留。
4. 填寫所需的欄位。 符合所選屬性的現有或全新單一資料庫、彈性集區或受控執行個體，就有資格獲得保留容量折扣。 取得折扣的 SQL Database 執行個體實際數目取決於選取的範圍和數量。
    ![提交 SQL Database 保留容量購買前的螢幕擷取畫面](./media/sql-database-reserved-vcores/sql-reserved-vcores-purchase.png)

下表描述了必要欄位。

| 欄位      | 描述|
|------------|--------------|
|訂用帳戶|用來支付 SQL Database 保留容量費用的訂用帳戶。 SQL Database 保留容量的預付費用會透過訂用帳戶的付款方式收取。 訂閱類型必須是企業協定（產品/服務編號：MS-AZR-0017P 或 MS-AZR-0148P）或具有即用即付定價的單個協定（產品/服務編號：MS-AZR-0003P 或 MS-AZR-0023P）。 針對企業訂用帳戶，費用會從註冊的承諾用量金額餘額扣除或作為超額部分收費。 對於具有即用即付定價的個人訂閱，費用將計入訂閱上的信用卡或發票付款條件。|
|影響範圍       |虛擬核心保留容量範圍可以涵蓋一個訂用帳戶或多個訂用帳戶 (共用範圍)。 如果您選取： <br/><br/>**共用**，vCore 預留折扣應用於在計費上下文中任何訂閱中運行的 SQL 資料庫實例。 針對企業客戶，共用範圍是註冊，並包含註冊中的所有訂用帳戶。 針對隨用隨付客戶，共用範圍是帳戶系統管理員所建立的所有隨用隨付訂用帳戶。<br/><br/>**單個訂閱**，vCore 預留折扣應用於此訂閱中的 SQL 資料庫實例。 <br/><br/>**單個資源組**，預留折扣應用於所選訂閱中的 SQL 資料庫實例以及該訂閱中的選定資源組。|
|區域      |SQL Database 保留容量所涵蓋的 Azure 區域。|
|部署類型|您要為其購買保留容量的 SQL 資源類型。|
|效能層級|SQL Database 執行個體的服務層級。
|詞彙        |一年或三年。|
|數量    |在 SQL 資料庫預留容量預留中購買的計算資源量。 數量是所選 Azure 區域和"性能"層中保留的 vCore 的多個 vCore，它們將獲得計費折扣。 例如，如果您正在運行或計畫運行 SQL 資料庫實例，總計算容量為 En5 16 vCores 在美國東部區域，那麼您將數量指定為 16，以最大化所有實例的好處。 |

1. 檢閱 [成本]**** 區段中 SQL Database 保留容量的成本。
1. 選取 [購買]****。
1. 選取 [檢視此保留]**** 以查看您的購買狀態。

## <a name="cancel-exchange-or-refund-reservations"></a>取消、交換保留或進行退費

您可以取消、交換保留或進行退費，但有某些限制。 如需詳細資訊，請參閱 [Azure 保留的自助式交換和退費](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)。

## <a name="vcore-size-flexibility"></a>vCore 大小彈性

vCore 大小彈性可協助您在效能層級和區域內相應增加或相應減少，而不會失去保留容量優勢。 SQL Database 保留容量為您提供彈性地在集區與單一資料庫之間暫時移動最忙碌的資料庫，作為一般作業 (在相同的區域和效能層級) 的一部分，而不會失去保留容量優勢。 透過在保留區中保留未套用的緩衝區，您可以在不超出您預算的情況下有效地管理效能高峰。

## <a name="limitation"></a>限制

您不能保留基於 DTU 的 SQL 資料庫（基本資料庫、標準資料庫或高級資料庫）。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡

如果您有疑問或需要幫助，[請創建支援請求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>後續步驟

虛擬核心保留容量折扣會自動套用至符合 SQL Database 保留容量範圍和屬性的 SQL Database 執行個體數目。 您可以透過 [Azure 入口網站](https://portal.azure.com)、PowerShell、CLI 或 API 來更新 SQL Database 保留容量的範圍。

若要了解如何管理 SQL Database 保留的容量保留，請參閱[管理 SQL Database 保留容量](../cost-management-billing/reservations/manage-reserved-vm-instance.md)。

若要深入了解 Azure 保留項目，請參閱下列文章：

- [什麼是 Azure 保留項目？](../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [管理 Azure 預留](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [了解 Azure 保留折扣](../cost-management-billing/reservations/understand-reservation-charges.md)
- [了解隨用隨付訂用帳戶的保留使用量](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [了解 Enterprise 註冊的保留項目使用量](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [合作夥伴中心雲端解決方案提供者 (CSP) 計畫中的 Azure 保留項目](https://docs.microsoft.com/partner-center/azure-reservations)

---
title: 調整資源
description: 本文說明如何藉由新增或移除已配置的資源，在 Azure SQL Database 和 SQL 受控執行個體中調整您的資料庫。
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnik, sstein
ms.date: 06/25/2019
ms.openlocfilehash: 026c2b7b57929d31fbbf776d81ee41eb73b73d44
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91321508"
---
# <a name="dynamically-scale-database-resources-with-minimal-downtime"></a>在最短停機時間的情況下動態調整資料庫資源
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database 和 SQL 受控執行個體可讓您以極短的 [停機時間](https://azure.microsoft.com/support/legal/sla/sql-database)，以動態方式將更多資源新增至您的資料庫。不過，在一段時間內會有一段時間切換到資料庫的連線中斷，您可以使用重試邏輯來減輕這些問題。

## <a name="overview"></a>概觀

當您的應用程式需求從幾個裝置和客戶成長到上百萬個裝置和客戶時，Azure SQL Database 和 SQL 受控執行個體在最短停機時間內即時調整規模。 擴充性是平臺即服務的最重要特性之一 (PaaS) ，可讓您在需要時，動態地將更多資源新增至您的服務。 Azure SQL Database 可讓您輕鬆地變更配置給您資料庫的資源 (CPU 電源、記憶體、IO 輸送量和儲存體)。

避免效能問題的原因可能是您無法使用索引編製或查詢重寫方法固定之應用程式的使用量增加。 新增更多資源，可讓您在資料庫達到目前資源限制且需要更強大功能來處理內送工作負載時快速回應。 Azure SQL Database 也可讓您相應減少不需要的資源，以降低成本。

您不需要擔心購買硬體以及變更基礎結構。 使用滑杆可以輕鬆地透過 Azure 入口網站來調整資料庫。

![調整資料庫效能](./media/scale-resources/scale-performance.svg)

Azure SQL Database 提供以 [DTU 為基礎的購買模型](service-tiers-dtu.md) 和以 [vCore 為基礎的購買](service-tiers-vcore.md)模型，而 Azure SQL 受控執行個體只提供以 [vCore 為基礎的購買模型](service-tiers-vcore.md)。 

- 以 [DTU 為基礎的購買模型](service-tiers-dtu.md) 提供三個服務層級的計算、記憶體和 i/o 資源混合，以支援輕量的資料庫工作負載：基本、標準和 Premium。 在各層內的效能層級分別提供這些資源的不同混用方式，而且您可以為其新增額外的儲存體資源。
- [以虛擬核心為基礎的購買模型](service-tiers-vcore.md)可讓您選擇虛擬核心的數目、記憶體數量，以及儲存體的數量和速度。 此購買模型提供三個服務層級：一般用途、業務關鍵和超大規模。

您可以透過基本、標準或一般用途服務層級，以較低的每月成本在小型的單一資料庫上建置您的第一個應用程式，並在日後隨時以手動或程式設計方式將服務層級變更為進階或業務關鍵服務層級，以符合您的解決方案需求。 您的應用程式或客戶皆無須停機，即可調整效能。 動態延展性可讓您的資料庫以透明的方式回應快速變化的資源需求，並且讓您只需支付您所需的資源費用。

> [!NOTE]
> 動態延展性與自動調整規模不同。 自動調整規模是指當服務根據準則自動調整時，動態調整可讓您以極短的停機時間進行手動調整。

Azure SQL Database 中的單一資料庫支援手動動態調整，但不支援自動調整規模。 如需「自動」** 體驗，請考慮使用彈性集區，以根據個別資料庫的需求讓資料庫共用集區中的資源。
不過，有一些腳本可協助針對 Azure SQL Database 中的單一資料庫，自動調整規模。 如需範例，請參閱[使用 PowerShell 來監視和調整單一 SQL 資料庫的規模](scripts/monitor-and-scale-database-powershell.md)。

您可以在應用程式停機時間最短 (通常平均少於四秒) 的情況下，隨時變更 [DTU 服務層級](service-tiers-dtu.md)或[虛擬核心特性](resource-limits-vcore-single-databases.md)。 對於許多企業和應用程式而言，只要能夠建立資料庫，並依需求調高或調低的效能即可，尤其是當使用模式相當容易預測時更是如此。 但如果您有無法預測的使用模式，則管理成本和商務模式就會變得相當困難。 針對此案例您可使用彈性集區，以在集區中的多個資料庫之間共用特定數量的 eDTU。

![SQL Database 簡介：不同層級和等級的單一資料庫 DTU](./media/scale-resources/single_db_dtus.png)

Azure SQL Database 提供動態調整資料庫的功能：

- 在[單一資料庫](single-database-scale.md)中，您可以使用 [DTU](resource-limits-dtu-single-databases.md) 或 [vCore](resource-limits-vcore-single-databases.md) 模型定義將指派給每個資料庫的資源數量上限。
- [彈性集區](elastic-pool-scale.md)可讓您定義集區中每個資料庫群組的資源上限。

Azure SQL 受控執行個體也可讓您進行調整： 

- [SQL 受控執行個體](../managed-instance/sql-managed-instance-paas-overview.md) 使用 [虛擬核心](../managed-instance/sql-managed-instance-paas-overview.md#vcore-based-purchasing-model) 模式，可讓您定義配置給您實例的 CPU 核心上限和儲存體上限。 受控實例中的所有資料庫都會共用配置給實例的資源。

在任何一種情況下起始擴大或縮小動作將會重新開機 database engine 進程，並視需要將它移至不同的虛擬機器。 將資料庫引擎進程移至新的虛擬機器是 **線上** 程式，您可以在程式進行時，繼續使用現有的 Azure SQL Database 服務。 目標資料庫引擎完全初始化並準備好處理查詢之後，連接將會 [從來源切換至目標資料庫引擎](single-database-scale.md#impact)。

> [!NOTE]
> 當擴大/縮小程式完成時，您可以預期會有短暫的連接中斷。 如果您已 [針對標準暫時性錯誤執行重試邏輯](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)，您將不會注意到容錯移轉。

## <a name="alternative-scale-methods"></a>替代調整方法

調整資源是改善資料庫效能的最簡單且最有效的方法，而不需要變更資料庫或應用程式程式碼。 在某些情況下，即使是最高的服務層級、計算大小和效能優化，也可能無法以成功且符合成本效益的方式處理您的工作負載。 在此情況下，您可以使用下列額外選項來調整資料庫：

- [讀取向外](read-scale-out.md) 延展是您取得資料的一個唯讀複本的可用功能，您可以在其中執行要求的唯讀查詢，例如報表。 唯讀複本會處理您的唯讀工作負載，而不會影響主資料庫上的資源使用方式。
- [資料庫分區](elastic-scale-introduction.md)是一組技術，可讓您將資料分割成數個資料庫，並分別調整它們。

## <a name="next-steps"></a>後續步驟

- 如需變更資料庫程式碼以改善資料庫效能的資訊，請參閱[尋找和套用效能建議](database-advisor-find-recommendations-portal.md)。
- 如需讓內建資料庫智慧最佳化資料庫的資訊，請參閱[自動調整](automatic-tuning-overview.md)。
- 如需 Azure SQL Database 中讀取相應放大的相關資訊，請參閱如何 [使用唯讀複本對唯讀查詢工作負載進行負載平衡](read-scale-out.md)。
- 如需資料庫分區的資訊，請參閱[使用 Azure SQL Database 相應放大](elastic-scale-introduction.md)。

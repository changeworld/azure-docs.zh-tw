---
title: 自訂 Azure 遷移伺服器評估評估 |微軟文檔
description: 描述如何自訂使用 Azure 遷移伺服器評估創建的評估
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: 2cfac978b0a5af20e9e2fa1e32a7361488f20fbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "68234278"
---
# <a name="customize-an-assessment"></a>自訂評量

本文介紹如何自訂由 Azure 遷移伺服器評估創建的評估。

[Azure Migrate](migrate-services-overview.md) 提供集中式中樞，可追蹤內部部署應用程式和工作負載及私人/公用雲端 VM 的探索、評量和移轉 (以 Azure 為目標)。 該中樞能提供 Azure Migrate 工具以進行評估和移轉，也提供協力廠商獨立軟體廠商 (ISV) 的供應項目。

可以使用 Azure 遷移伺服器評估工具為本地 VMware VM 和超 VM 創建評估，為遷移到 Azure 做準備。

## <a name="about-assessments"></a>關於評估

您可以使用 Azure Migrate 伺服器評估來執行的評估有兩種。

**評量** | **詳細資料** | **資料**
--- | --- | ---
**以效能為基礎** | 以所收集效能資料為基礎的評估 | **建議的 VM 大小**：基於 CPU 和記憶體利用率資料。<br/><br/> **推薦的磁片類型（標準磁片或高級託管磁片）：** 基於 IOPS 和本地磁片的輸送量。
**作為內部部署** | 以內部部署大小調整為基礎的評估。 | **建議的 VM 大小**：基於本地 VM 大小<br/><br> **建議的磁片類型**：根據為評估選擇的存儲類型設置。


## <a name="how-is-an-assessment-done"></a>如何進行評估？

在 Azure 遷移伺服器評估中執行的評估分為三個階段。 評量會從適用性分析開始，然後是調整大小，最後是每月成本估計。 機器必須先通過前一個評量，才可前往下一階段。 舉例而言，如果機器未通過 Azure 適用性檢查，就會標示為不適合 Azure，而不會再去計算大小和成本。 [深入了解。](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation)

## <a name="whats-in-an-assessment"></a>評估包含什麼？

**屬性** | **詳細資料**
--- | ---
**目標位置** | 要作為移轉目的地的 Azure 位置。<br/> 伺服器評估目前支援這些目的地區域：澳大利亞東部、澳大利亞東南部、巴西南部、加拿大中部、加拿大東部、印度中部、美國中部、中國東部、中國北部、東亞、美國東部、美國東部、德國中部、德國東北部、日本東部、日本西部、韓國中部、韓國南部、美國中北部、北歐、美國中南部、東南亞、南印度、英國南部、英國西部、美國亞利桑那州政府、美國德克薩斯州政府、美國弗吉尼亞州西部、美國中西部、西歐、西印度、美國西部和美國西部2。
**儲存體類型** | 可以使用此屬性在 Azure 中指定要移動到的磁片類型。<br/><br/> 對於本地部署大小調整，可以將目標存儲類型指定為高級託管磁片、標準 SSD 託管磁片或標準 HDD 管理磁片。 對於基於性能的尺寸調整，可以將目標磁片類型指定為自動磁片、高級管理磁片、標準硬碟管理磁片或標準 SSD 管理磁片。<br/><br/> 將儲存體類型指定為自動時，將根據磁碟的效能資料 (IOPS 和輸送量) 完成磁碟建議。 如果將存儲類型指定為高級/標準，評估將建議在所選存儲類型內使用磁片 SKU。 如果要實現 99.9% 的單實例 VM SLA，則可能需要將存儲類型指定為高級託管磁片。 這可確保建議評量中的所有磁碟成為進階受控磁碟。 Azure
**保留實例 （RI）** | 此屬性可説明您指定 Azure 中是否有[預留實例](https://azure.microsoft.com/pricing/reserved-vm-instances/)，然後評估中的成本估計將採用 RI 折扣。 Azure Migrate 中的隨用隨付優惠目前僅支援保留執行個體。
**調整大小準則** | 用於對 Azure 的 VM 大小調整的條件。 您可以執行*基於性能*的大小調整，也可以將 VM*大小調整為本地*，而無需考慮性能歷史記錄。
**效能歷程記錄** | 用於評估電腦效能資料的持續時間。 此屬性僅在大小調整條件基於*性能*時適用。
**百分位數使用率** | 要視為適當大小調整的效能取樣集百分位數值。 此屬性僅在大小調整基於*性能*時適用。
**VM 系列** |     您可以指定考慮要正確調整大小的 VM 系列。 例如，如果您不打算將生產環境移轉至 Azure 中的 A 系列虛擬機器，則可以從清單或系列排除 A 系列，而正確調整大小只會在選取的系列中完成。
**緩和因數** | Azure 遷移伺服器評估會考慮評估期間的緩衝區（舒適係數）。 此緩衝區適用於 VM 的機器使用量資料 (CPU、記憶體、磁碟和網路)。 緩和因數會考量各個問題，例如季節性使用量、簡短的效能歷程記錄，以及未來可能增加的使用量。<br/><br/> 以 10 核心的 VM 為例，如果使用率為 20%，一般即相當於一個 2 核心的 VM。 但如果緩和因數為 2.0 x，則結果為 4 核心的 VM。
**提供** | 您所註冊的 [Azure 供應項目](https://azure.microsoft.com/support/legal/offer-details/)。 Azure Migrate 會據以預估成本。
**貨幣** | 帳單貨幣。
**折扣 (%)** | 針對 Azure 供應項目所能獲得的任何訂用帳戶特定折扣。<br/> 預設設定為 0%。
**VM 運作時間** | 如果 VM不會在 Azure 中全天候執行，您可以指定其執行的持續時間 (每月天數和每天時數)，成本預估會據此來計算。<br/> 預設值是每月 31 天和每天 24 小時。
**Azure Hybrid Benefit** | 指定您是否具有軟體保證並符合 Azure[混合權益的條件](https://azure.microsoft.com/pricing/hybrid-use-benefit/)。 如果設定為 [是]，則會考慮對 Windows VM 使用非 Windows Azure 價格。 預設值為 [是]。


## <a name="edit-assessment-properties"></a>編輯評量屬性

要在創建評估後編輯評估屬性，請執行以下操作：

1. 在 Azure 遷移專案中，按一下 **"伺服器**"。
2. 在**Azure 遷移：伺服器評估**中，按一下評估計數。
3. 在**評估**中，按一下>**編輯屬性**的相關評估。
5. 根據上表自訂評估屬性。
6. 按一下 [儲存]**** 以更新評估。


您還可以在創建評估時編輯評估屬性。


## <a name="next-steps"></a>後續步驟

[深入了解](concepts-assessment-calculation.md)評定的計算方式。

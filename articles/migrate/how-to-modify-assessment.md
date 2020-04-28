---
title: 自訂 Azure Migrate Server 評估的評量 |Microsoft Docs
description: 說明如何自訂使用 Azure Migrate Server 評估所建立的評量
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: 2cfac978b0a5af20e9e2fa1e32a7361488f20fbe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "68234278"
---
# <a name="customize-an-assessment"></a>自訂評量

本文說明如何自訂 Azure Migrate Server 評估所建立的評量。

[Azure Migrate](migrate-services-overview.md) 提供集中式中樞，可追蹤內部部署應用程式和工作負載及私人/公用雲端 VM 的探索、評量和移轉 (以 Azure 為目標)。 該中樞能提供 Azure Migrate 工具以進行評估和移轉，也提供協力廠商獨立軟體廠商 (ISV) 的供應項目。

您可以使用 Azure Migrate Server 評估工具來建立內部部署 VMware Vm 和 Hyper-v Vm 的評量，以準備遷移至 Azure。

## <a name="about-assessments"></a>關於評量

您可以使用 Azure Migrate 伺服器評估來執行的評估有兩種。

**評量** | **詳細資料** | **Data**
--- | --- | ---
**以效能為基礎** | 以所收集效能資料為基礎的評估 | **建議的 VM 大小**：以 CPU 和記憶體使用量資料為基礎。<br/><br/> **建議的磁碟類型 (標準或進階受控磁碟**)：以內部部署磁碟的 IOPS 和輸送量為基礎。
**作為內部部署** | 以內部部署大小調整為基礎的評估。 | **建議的 VM 大小**：以內部部署 VM 大小為基礎<br/><br> **建議的磁碟類型**：以您為評估選取的儲存體類型設定為基礎。


## <a name="how-is-an-assessment-done"></a>評估如何完成？

在 Azure Migrate Server 評估中完成的評量有三個階段。 評量會從適用性分析開始，然後是調整大小，最後是每月成本估計。 機器必須先通過前一個評量，才可前往下一階段。 舉例而言，如果機器未通過 Azure 適用性檢查，就會標示為不適合 Azure，而不會再去計算大小和成本。 [深入了解。](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation)

## <a name="whats-in-an-assessment"></a>評估包含什麼？

**屬性** | **詳細資料**
--- | ---
**目標位置** | 要作為移轉目的地的 Azure 位置。<br/> 伺服器評估目前支援下列目的地區域：澳大利亞東部、澳大利亞東南部、巴西南部、加拿大中部、加拿大東部、印度中部、美國中部、中國東部、中國北部、東亞、美國東部、東部美國2、德國中部、德國東北部、日本東部、日本西部、韓國中部、南韓南部、美國中北部、北歐、美國中南部、東南亞、印度南部、英國南部、英國西部、US Gov 亞利桑那州、US Gov 德克薩斯州、US Gov 維吉尼亞州、美國中西部、西歐、印度西部、美國西部和 West 美國2。
**儲存類型** | 您可以使用此屬性，在 Azure 中指定您想要移至的磁片類型。<br/><br/> 針對「內部部署」大小調整，您可以將目標儲存類型指定為高階受控磁片、標準 SSD 受控磁片或標準 HDD 受控磁片。 針對以效能為基礎的大小調整，您可以將目標磁片類型指定為自動、高階受控磁片、標準 HDD 受控磁片，或標準 SSD 受控磁片。<br/><br/> 將儲存體類型指定為自動時，將根據磁碟的效能資料 (IOPS 和輸送量) 完成磁碟建議。 如果您將儲存體類型指定為 [premium/standard]，則評量會建議所選儲存體類型內的磁片 SKU。 如果您想要達到99.9% 的單一實例 VM SLA，您可能會想要將儲存體類型指定為 Premium 受控磁片。 這可確保建議評量中的所有磁碟成為進階受控磁碟。 Azure
**保留實例（RI）** | 此屬性可協助您指定在 Azure 中是否有[保留實例](https://azure.microsoft.com/pricing/reserved-vm-instances/)，評估中的成本預估會完成 RI 折扣。 Azure Migrate 中的隨用隨付優惠目前僅支援保留執行個體。
**調整大小準則** | 要用來為 Azure 適當調整 Vm 大小的準則。 您可以執行以*效能為基礎*的大小調整，或將 vm 大小設*為內部部署*，而不考慮效能歷程記錄。
**效能歷程記錄** | 評估機器效能資料時所要考慮的持續時間。 只有當調整大小準則是以*效能為基礎*時，才適用此屬性。
**百分位數使用率** | 要視為適當大小調整的效能取樣集百分位數值。 只有在調整大小是以*效能為基礎*時，才適用此屬性。
**VM 系列** |     您可以指定考慮要正確調整大小的 VM 系列。 例如，如果您不打算將生產環境移轉至 Azure 中的 A 系列虛擬機器，則可以從清單或系列排除 A 系列，而正確調整大小只會在選取的系列中完成。
**緩和因數** | Azure Migrate Server 評估會考慮在評估期間的緩衝區（緩和因數）。 此緩衝區適用於 VM 的機器使用量資料 (CPU、記憶體、磁碟和網路)。 緩和因數會考量各個問題，例如季節性使用量、簡短的效能歷程記錄，以及未來可能增加的使用量。<br/><br/> 以 10 核心的 VM 為例，如果使用率為 20%，一般即相當於一個 2 核心的 VM。 但如果緩和因數為 2.0 x，則結果為 4 核心的 VM。
**供應項目** | 您所註冊的 [Azure 供應項目](https://azure.microsoft.com/support/legal/offer-details/)。 Azure Migrate 會據以預估成本。
**貨幣** | 帳單貨幣。
**折扣 (%)** | 針對 Azure 供應項目所能獲得的任何訂用帳戶特定折扣。<br/> 預設設定為 0%。
**VM 運作時間** | 如果 VM不會在 Azure 中全天候執行，您可以指定其執行的持續時間 (每月天數和每天時數)，成本預估會據此來計算。<br/> 預設值是每月 31 天和每天 24 小時。
**Azure Hybrid Benefit** | 指定您是否擁有軟體保證，以及是否符合[Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/)的資格。 如果設定為 [是]，則會考慮對 Windows VM 使用非 Windows Azure 價格。 預設值為 [是]。


## <a name="edit-assessment-properties"></a>編輯評量屬性

若要在建立評量之後編輯評估屬性，請執行下列動作：

1. 在 Azure Migrate 專案中，按一下 [**伺服器**]。
2. 在 [ **Azure Migrate：伺服器評估**] 中，按一下 [評量] 計數。
3. 在 [**評**量] 中，按一下相關的評估 >**編輯屬性**]。
5. 根據上表自訂評量屬性。
6. 按一下 [儲存]**** 以更新評估。


您也可以在建立評量時編輯評估屬性。


## <a name="next-steps"></a>後續步驟

[深入了解](concepts-assessment-calculation.md)評定的計算方式。

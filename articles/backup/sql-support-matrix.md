---
title: Azure 備份 Azure Vm 中 SQL Server 備份的支援矩陣
description: 提供使用 Azure 備份服務在 Azure Vm 中備份 SQL Server 時的支援設定和限制摘要。
ms.topic: conceptual
ms.date: 03/05/2020
ms.custom: references_regions
ms.openlocfilehash: 41511abaa071bd0f64ee699c52486b71ec036a68
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87926445"
---
# <a name="support-matrix-for-sql-server-backup-in-azure-vms"></a>Azure Vm 中 SQL Server 備份的支援矩陣

您可以使用 Azure 備份來備份 Microsoft Azure 雲端平臺上裝載的 Azure Vm 中 SQL Server 資料庫。 本文摘要說明 Azure Vm 中 SQL Server 備份的案例和部署的一般支援設定和限制。

## <a name="scenario-support"></a>案例支援

**支援** | **詳細資料**
--- | ---
**支援的部署** | 支援 SQL Marketplace Azure VM 和非 Marketplace (手動安裝 SQL Server) VM。
**支援區域** | 澳大利亞東南部 (ASE)、澳大利亞東部 (AE)、澳大利亞中部 (AC)、澳大利亞中部 2 (AC) <br> 巴西南部 (BRS)<br> 加拿大中部 (CNC)、加拿大東部 (CE)<br> 東南亞 (SEA)、東亞 (EA) <br> 美國東部 (EUS)、美國東部 2 (EUS2)、美國中西部 (WCUS)、美國西部 (WUS)、美國西部 2 (WUS2)、美國中北部 (NCUS)、美國中部 (CUS)、美國中南部 (SCUS) <br> 印度中部 (INC)、印度南部 (INS)、印度西部 <br> 日本東部 (JPE)、日本西部 (JPW) <br> 南韓中部 (KRC)、南韓南部 (KRS) <br> 北歐 (NE)、西歐 <br> 英國南部 (UKS)、英國西部 (UKW) <br> US Gov 亞利桑那州、US Gov 維吉尼亞州、US Gov 德克薩斯州、US DoD 中部、US DoD 東部 <br> 德國北部、德國中西部 <br> 瑞士北部、瑞士西部 <br> 法國中部 <br> 中國東部、中國東部 2、中國北部、中國北部 2
**支援的作業系統** | Windows Server 2019、Windows Server 2016、Windows Server 2012、Windows Server 2008 R2 SP1 <br/><br/> 目前不支援 Linux。
**支援的 SQL Server 版本** | SQL Server 2019、SQL Server 2017 (如 [搜尋產品生命週期](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017) 頁面所述)、SQL Server 2016 and SPs (如 [搜尋產品生命週期](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack) 頁面所述)、SQL Server 2014、SQL Server 2012、SQL Server 2008 R2、SQL Server 2008 <br/><br/> Enterprise、Standard、Web、Developer、Express。
**支援的 .NET 版本** | 安裝在 VM 上的 .NET Framework 4.5.2 或更新版本

## <a name="feature-considerations-and-limitations"></a>功能考慮和限制

|設定  |上限 |
|---------|---------|
|可以在伺服器 (和保存庫中保護的資料庫數目)     |   2000      |
| (的資料庫大小超過此值，可能會導致效能問題)    |   2 TB      |
|資料庫中支援的檔案數目    |   1000      |

>[!NOTE]
> [下載詳細的資源規劃](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) 工具，以根據 VM 資源、頻寬和備份原則來計算建議的每一部伺服器所建議的大約受保護資料庫數目。

* 您可以透過 Azure 入口網站或 **PowerShell** 來設定 SQL Server 備份。 不支援 CLI。
* 有兩種[部署](../azure-resource-manager/management/deployment-models.md)支援此解決方案 - Azure Resource Manager VM 和傳統 VM。
* 所有備份類型 (完整/差異/記錄) 和復原模式，都支援 (簡單/完整/大量記錄) 。
* **唯讀**資料庫支援完整和僅複製完整備份類型。
* 如果使用者在備份原則中明確啟用，則支援 SQL 原生壓縮。 Azure 備份會使用壓縮/NO_COMPRESSION 子句來覆寫實例層級的預設值，這取決於使用者設定的這個控制項值。
* 支援啟用 TDE 的資料庫備份。 若要將 TDE 加密的資料庫還原到另一個 SQL Server，您必須先將 [憑證還原至目的地伺服器](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server)。 SQL Server 2016 和更新版本之已啟用 TDE 資料庫的備份壓縮可供使用，但以較低的傳輸大小為限[，如下所述。](https://techcommunity.microsoft.com/t5/sql-server/backup-compression-for-tde-enabled-databases-important-fixes-in/ba-p/385593)
* 不支援鏡像資料庫和資料庫快照集的備份和還原作業。
* 不支援 SQL Server **容錯移轉叢集實例 (FCI) ** 。
* 使用多個備份解決方案來備份獨立 SQL Server 實例或 SQL Always on 可用性群組可能會導致備份失敗。 避免這樣做。 使用相同或不同的解決方案來備份可用性群組的兩個節點，也可能導致備份失敗。
* 設定可用性群組時，會根據幾個因素從不同的節點取得備份。 可用性群組的備份行為摘述於下方。

### <a name="back-up-behavior-with-always-on-availability-groups"></a>Alaways On 可用性群組的備份行為

建議您只在可用性群組 (AG) 的一個節點上設定備份。 一律在與主要節點相同的區域中設定備份。 換句話說，您一律需要主要節點出現在您要設定備份的區域中。 如果 AG 的所有節點都在已設定備份的相同區域中，就不會有任何疑慮。

#### <a name="for-cross-region-ag"></a>若為跨區域 AG

* 無論備份喜好設定為何，備份只會從已設定備份的相同區域中的節點執行。 這是因為不支援跨區域備份。 如果您只有兩個節點，而第二個節點位於其他區域，則備份將會繼續從主要節點執行 (除非您的備份喜好設定為「僅限次要」 ) 。
* 如果節點容錯移轉至不同于設定備份的區域，則備份將會在容錯移轉區域中的節點上失敗。

視備份喜好設定和備份類型 (完整/差異/記錄/只複製完整) 而定，會從特定節點 (主要/次要) 進行備份。

#### <a name="backup-preference-primary"></a>備份喜好設定：主要

**備份類型** | **節點**
--- | ---
完整 | 主要
差異 | 主要
Log |  主要
只複製完整 |  主要

#### <a name="backup-preference-secondary-only"></a>備份喜好設定：僅限次要

**備份類型** | **節點**
--- | ---
完整 | 主要
差異 | 主要
Log |  次要
只複製完整 |  次要

#### <a name="backup-preference-secondary"></a>備份喜好設定：次要

**備份類型** | **節點**
--- | ---
完整 | 主要
差異 | 主要
Log |  次要
只複製完整 |  次要

#### <a name="no-backup-preference"></a>沒有備份喜好設定

**備份類型** | **節點**
--- | ---
完整 | 主要
差異 | 主要
Log |  次要
只複製完整 |  次要

## <a name="next-steps"></a>後續步驟

瞭解如何備份在 Azure VM 上 [執行的 SQL Server 資料庫](backup-azure-sql-database.md) 。

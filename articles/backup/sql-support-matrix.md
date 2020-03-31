---
title: Azure 備份支援矩陣，用於 Azure VM 中的 SQL 伺服器備份
description: 提供使用 Azure 備份服務在 Azure VM 中備份 SQL Server 時的支援設置和限制摘要。
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 79a7e30ab9240c489a66b547ff85bea7887131b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409995"
---
# <a name="support-matrix-for-sql-server-backup-in-azure-vms"></a>支援 Azure VM 中的 SQL 伺服器備份矩陣

可以使用 Azure 備份在 Microsoft Azure 雲平臺託管的 Azure VM 中備份 SQL Server 資料庫。 本文總結了 Azure VM 中 SQL Server 備份的方案和部署的一般支援設置和限制。

## <a name="scenario-support"></a>案例支援

**支援** | **詳細資料**
--- | ---
**支援的部署** | 支援 SQL Marketplace Azure VM 和非 Marketplace (手動安裝 SQL Server) VM。
**支援的地區** | 澳大利亞東南部 (ASE)、澳大利亞東部 (AE)、澳大利亞中部 (AC)、澳大利亞中部 2 (AC) <br> 巴西南部 (BRS)<br> 加拿大中部 (CNC)、加拿大東部 (CE)<br> 東南亞 (SEA)、東亞 (EA) <br> 美國東部 (EUS)、美國東部 2 (EUS2)、美國中西部 (WCUS)、美國西部 (WUS)、美國西部 2 (WUS2)、美國中北部 (NCUS)、美國中部 (CUS)、美國中南部 (SCUS) <br> 印度中部 (INC)、印度南部 (INS)、印度西部 <br> 日本東部 (JPE)、日本西部 (JPW) <br> 南韓中部 (KRC)、南韓南部 (KRS) <br> 北歐 (NE)、西歐 <br> 英國南部 (UKS)、英國西部 (UKW) <br> US Gov 亞利桑那州、US Gov 維吉尼亞州、US Gov 德克薩斯州、US DoD 中部、US DoD 東部 <br> 德國北部、德國中西部 <br> 瑞士北部、瑞士西部 <br> 法國中部 <br> 中國東部、中國東部 2、中國北部、中國北部 2
**支援的作業系統** | Windows Server 2019、Windows Server 2016、Windows Server 2012、Windows Server 2008 R2 SP1 <br/><br/> 目前不支援 Linux。
**支援的 SQL Server 版本** | SQL Server 2019、SQL Server 2017 (如 [搜尋產品生命週期](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017) 頁面所述)、SQL Server 2016 and SPs (如 [搜尋產品生命週期](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack) 頁面所述)、SQL Server 2014、SQL Server 2012、SQL Server 2008 R2、SQL Server 2008 <br/><br/> Enterprise、Standard、Web、Developer、Express。
**支援的 .NET 版本** | 安裝在 VM 上的 .NET Framework 4.5.2 或更新版本

## <a name="feature-consideration-and-limitations"></a>功能考量和限制

* 您可以透過 Azure 入口網站或 **PowerShell** 來設定 SQL Server 備份。 我們不支援 CLI。
* 有兩種[部署](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model)支援此解決方案 - Azure Resource Manager VM 和傳統 VM。
* 執行 SQL Server 的 VM 需要有網際網路連線能力，才能存取 Azure 公用 IP 位址。
* 不支援 SQL 伺服器**容錯移轉叢集實例 （FCI）。**
* 不支援鏡像資料庫和資料庫快照集的備份和還原作業。
* 使用多個備份解決方案來備份獨立 SQL Server 執行個體或 SQL Always On 可用性群組可能會導致備份失敗；請避免這麼做。
* 使用相同或不同的解決方案來備份可用性群組的兩個節點，也可能導致備份失敗。
* 「Azure 備份」針對**唯讀**資料庫僅支援「完整」和「只複製完整」備份
* 無法保護含有大量檔案的資料庫。 支援的檔案數目上限為 **1000** 個。  
* 您可以在保存庫中備份最多 **+2000**個 SQL Server 資料庫。 如果您有更多資料庫，則可以建立多個保存庫。
* 您最多可以一次設定 **50** 個資料庫的備份；此限制有助於將備份負載最佳化。
* 我們支援高達 2 **TB**的資料庫;大於該性能問題的大小。
* 要瞭解每個伺服器可以保護多少個資料庫，請考慮頻寬、VM 大小、備份頻率、資料庫大小等因素。 [下載](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx)資源規劃器，根據 VM 資源和備份策略計算每個伺服器可以擁有的資料庫的大致數量。
* 配置可用性組後，根據幾個因素從不同的節點獲取備份。 可用性群組的備份行為摘述於下方。

### <a name="back-up-behavior-with-always-on-availability-groups"></a>使用"始終在可用性組"備份行為

我們建議僅在可用性組 （AG） 的一個節點上配置備份。 始終在同一區域與主節點配置備份。 換句話說，您始終需要主節點存在於配置備份的區域。 如果 AG 的所有節點都位於配置備份的同一區域，則沒有任何問題。

#### <a name="for-cross-region-ag"></a>若為跨區域 AG

* 無論備份首選項如何，備份都只會從配置備份的同一區域中的節點運行。 這是因為不支援跨區域備份。 如果只有兩個節點，而輔助節點位於其他區域中，則備份將繼續從主節點運行（除非您的備份首選項是"僅輔助節點"）。
* 如果節點容錯移轉到與配置備份的區域不同的區域，則容錯移轉區域中的節點上的備份將失敗。

視備份喜好設定和備份類型 (完整/差異/記錄/只複製完整) 而定，會從特定節點 (主要/次要) 進行備份。

#### <a name="backup-preference-primary"></a>備份首選項：主

**備份類型** | **節點**
--- | ---
完整 | Primary
差異 | Primary
Log |  Primary
只複製完整 |  Primary

#### <a name="backup-preference-secondary-only"></a>備份首選項：僅限輔助

**備份類型** | **節點**
--- | ---
完整 | Primary
差異 | Primary
Log |  次要
只複製完整 |  次要

#### <a name="backup-preference-secondary"></a>備份首選項：輔助

**備份類型** | **節點**
--- | ---
完整 | Primary
差異 | Primary
Log |  次要
只複製完整 |  次要

#### <a name="no-backup-preference"></a>沒有備份喜好設定

**備份類型** | **節點**
--- | ---
完整 | Primary
差異 | Primary
Log |  次要
只複製完整 |  次要

## <a name="next-steps"></a>後續步驟

瞭解如何[備份在](backup-azure-sql-database.md)Azure VM 上運行的 SQL Server 資料庫。

---
title: 支援 Azure Migrate 的實體伺服器評估
description: 瞭解 Azure Migrate 的實體伺服器評估支援。
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 057d384c14328deca2853e891f23250aa1d61702
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2020
ms.locfileid: "76154783"
---
# <a name="support-matrix-for-physical-server-assessment"></a>實體伺服器評估的支援矩陣 

您可以使用[Azure Migrate 服務](migrate-overview.md)來評估電腦，並將機器遷移至 Microsoft Azure 雲端。 本文摘要說明評估和遷移內部部署實體伺服器的支援設定和限制。


## <a name="overview"></a>概觀

若要使用這篇文章來評估內部部署機器以遷移至 Azure，請將 Azure Migrate：伺服器評估工具新增至 Azure Migrate 專案。 您會部署[Azure Migrate 設備](migrate-appliance.md)。 設備會持續探索內部部署機器，並將設定和效能資料傳送至 Azure。 在機器探索之後，您會將探索到的機器收集到群組中，並執行群組的評量

## <a name="limitations"></a>限制

**支援** | **詳細資料**
--- | ---
**評量限制**| 在單一[專案](migrate-support-matrix.md#azure-migrate-projects)中探索及評估最多35000個實體伺服器。
**專案限制** | 您可以在 Azure 訂用帳戶中建立多個專案。 專案可以包含 VMware Vm、Hyper-v Vm 和實體伺服器，最多可達評量限制。
**探索** | Azure Migrate 設備可以探索最多250部實體伺服器。
**評量** | 您最多可以在單一群組中新增35000部電腦。<br/><br/> 您可以在單一評估中評估多達35000部機器。

[深入瞭解](concepts-assessment-calculation.md)評量。




## <a name="physical-server-requirements"></a>實體伺服器需求

| **支援**                | **詳細資料**               
| :-------------------       | :------------------- |
| **實體伺服器部署**       | 實體伺服器可以是獨立或部署在叢集中。 |
| **權限**           | **Windows：** 在您要包含在探索中的所有 Windows 伺服器上設定本機使用者帳戶。 使用者帳戶必須新增至這些群組-遠端桌面使用者、效能監視器使用者和效能記錄使用者。 <br/> **Linux：** 您在要探索的 Linux 伺服器上需要根帳號。 |
| **作業系統** | 除了下列以外，所有的[Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)和[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)作業系統都受到支援：<br/> Windows Server 2003 <br/> SUSE Linux|


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate 設備需求

Azure Migrate 會使用[Azure Migrate 設備](migrate-appliance.md)來進行探索和評量。 實體伺服器的設備可以在 VM 或實體機器上執行。 您可以使用從 Azure 入口網站下載的 PowerShell 腳本進行設定。

- 瞭解實體伺服器的[設備需求](migrate-appliance.md#appliance---physical)。
- 瞭解設備需要存取的[url](migrate-appliance.md#url-access) 。

## <a name="port-access"></a>埠存取

下表摘要說明評量的通訊埠需求。

**裝置** | **[連接]**
--- | ---
**台** | TCP 通訊埠3389上的輸入連線，以允許應用裝置的遠端桌面連線。<br/> 埠44368上的輸入連線，可使用 URL 從遠端存取應用裝置管理應用程式： ``` https://<appliance-ip-or-name>:44368 ```<br/> 埠443、5671和5672上的輸出連線，以將探索和效能中繼資料傳送至 Azure Migrate。
**實體伺服器** | **Windows：** 埠443上的輸入連線、WinRM 埠5985（HTTP）和5986（HTTPS），以從 Windows 伺服器提取設定和效能中繼資料。 <br/> **Linux：** 埠22（UDP）上的輸入連線，可從 Linux 伺服器提取設定和效能中繼資料。 |

## <a name="agent-based-dependency-visualization"></a>以代理程式為基礎的相依性視覺效果

相依性[視覺效果](concepts-dependency-visualization.md)可協助您將要評估和遷移的機器相依性視覺化。 對於以代理程式為基礎的視覺效果，下表摘要說明需求和限制。


**需求** | **詳細資料**
--- | ---
**部署** | 部署相依性視覺效果之前，您應該先備妥 Azure Migrate 專案，並將 Azure Migrate： Server 評估工具加入至專案。 在設定 Azure Migrate 設備以探索您的內部部署機器之後，您會部署相依性視覺效果。<br/><br/> Azure Government 無法使用相依性視覺效果。
**服務對應** | 以代理程式為基礎的相依性視覺效果會使用[Azure 監視器記錄](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)中的[服務對應](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map)解決方案。<br/><br/> 若要部署，請將新的或現有的 Log Analytics 工作區與 Azure Migrate 專案產生關聯。
**Log Analytics 工作區** | 工作區必須與 Azure Migrate 專案位於相同的訂用帳戶中。<br/><br/> Azure Migrate 支援位於美國東部、東南亞和西歐區域的工作區。<br/><br/>  工作區必須位於[支援服務對應](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)的區域中。<br/><br/> 加入 Azure Migrate 專案的工作區之後，就無法修改它。
**費用** | 服務對應解決方案不會產生前180天的任何費用（從您建立 Log Analytics 工作區與 Azure Migrate 專案相關聯的那天開始計算）。<br/><br/> 經過 180 天後，會套用標準 Log Analytics 費用。<br/><br/> 在相關聯的 Log Analytics 工作區中使用服務對應以外的任何解決方案，將會產生標準 Log Analytics 費用。<br/><br/> 如果您刪除 Azure Migrate 專案，工作區不會隨之刪除。 刪除專案之後，服務對應不是免費的，而且每個節點都會依據 Log Analytics 工作區的付費層收費。
**代理程式** | 代理程式型相依性視覺效果需要在您要分析的每部機器上安裝兩個代理程式。<br/><br/> - [Microsoft Monitoring agent （MMA）](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)<br/><br/> - [Dependency agent](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent)。 
**網際網路連線** | 如果電腦未連線到網際網路，您需要在其上安裝 Log Analytics 閘道。

## <a name="next-steps"></a>後續步驟

[準備進行實體伺服器評估](tutorial-prepare-physical.md)。

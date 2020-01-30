---
title: 支援 Azure Migrate 中的 Hyper-v 評估
description: 瞭解 Azure Migrate 的 Hyper-v 評估支援。
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 9c1228992d71e56b9118e88967478e619c14959a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76834462"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Hyper-v 評估的支援矩陣

本文摘要說明使用[Azure Migrate：伺服器評估](migrate-services-overview.md#azure-migrate-server-assessment-tool)來評估 hyper-v vm 的支援設定和限制。 如果您要尋找將 Hyper-v Vm 遷移至 Azure 的相關資訊，請參閱[遷移支援矩陣](migrate-support-matrix-hyper-v-migration.md)。

## <a name="overview"></a>概觀

若要使用這篇文章來評估內部部署機器以遷移至 Azure，請將 Azure Migrate：伺服器評估工具新增至 Azure Migrate 專案。 您會部署[Azure Migrate 設備](migrate-appliance.md)。 設備會持續探索內部部署機器，並將設定和效能資料傳送至 Azure。 在機器探索之後，您會將探索到的機器收集到群組中，並執行群組的評量。


## <a name="limitations"></a>限制

**支援** | **詳細資料**
--- | ---
**評量限制**| 在單一[專案](migrate-support-matrix.md#azure-migrate-projects)中探索及評估最多35000個 hyper-v vm。
**專案限制** | 您可以在 Azure 訂用帳戶中建立多個專案。 專案可以包含 VMware Vm、Hyper-v Vm 和實體伺服器，最多可達評量限制。
**探索** | Azure Migrate 設備可以探索最多5000部 Hyper-v Vm。<br/><br/> 設備最多可以連接到300的 Hyper-v 主機。
**評量** | 您最多可以在單一群組中新增35000部電腦。<br/><br/> 您可以在單一評估中評估多達35000個 Vm。

[深入瞭解](concepts-assessment-calculation.md)評量。



## <a name="hyper-v-host-requirements"></a>Hyper-v 主機需求

| **支援**                | **詳細資料**               
| :-------------------       | :------------------- |
| **主機部署**       | Hyper-v 主機可以是獨立或部署在叢集中。 |
| **權限**           | 您需要 Hyper-v 主機的系統管理員許可權。 <br/> 或者，如果您不想要指派系統管理員許可權，請建立本機或網域使用者帳戶，並將使用者新增至這些群組-遠端系統管理使用者、Hyper-v 系統管理員和效能監視器使用者。 |
| **主機作業系統** | Windows Server 2019、Windows Server 2016 或 Windows Server 2012 R2。<br/> 您無法評估位於執行 Windows Server 2012 的 Hyper-V 主機上的 VM。 |
| **PowerShell 遠端處理**   | 必須在每部主機上啟用。 |
| **Hyper-v 複本**       | 如果您使用 Hyper-v 複本（或您有多個 vm 具有相同的 VM 識別碼），並使用 Azure Migrate 探索原始和複寫的 Vm，則 Azure Migrate 所產生的評量可能不正確。 |


## <a name="hyper-v-vm-requirements"></a>Hyper-v VM 需求

| **支援**                  | **詳細資料**               
| :----------------------------- | :------------------- |
| **作業系統** | Azure 支援的所有[Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)和[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)作業系統。 |
| **Integration Services**       | [Hyper-v Integration Services](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services)必須在您評估的 vm 上執行，才能捕捉作業系統資訊。 |


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate 設備需求

Azure Migrate 會使用[Azure Migrate 設備](migrate-appliance.md)來進行探索和評量。 Hyper-v 的應用裝置會在 Hyper-v VM 上執行，並使用您從 Azure 入口網站下載的壓縮 Hyper-v VHD 進行部署。 

- 瞭解 Hyper-v 的[設備需求](migrate-appliance.md#appliance---hyper-v)。
- 瞭解設備需要存取的[url](migrate-appliance.md#url-access) 。

## <a name="port-access"></a>埠存取

下表摘要說明評量的通訊埠需求。

**裝置** | **[連接]**
--- | ---
**台** | TCP 通訊埠3389上的輸入連線，以允許應用裝置的遠端桌面連線。<br/> 埠44368上的輸入連線，可使用 URL 從遠端存取應用裝置管理應用程式： ``` https://<appliance-ip-or-name>:44368 ```<br/> 埠443（HTTPS）、5671和5672（AMQP）上的輸出連線，以將探索和效能中繼資料傳送至 Azure Migrate。
**Hyper-v 主機/叢集** | WinRM 埠5985（HTTP）和5986（HTTPS）上的輸入連線，可使用通用訊息模型（CIM）會話提取 Hyper-v Vm 的設定和效能中繼資料。

## <a name="agent-based-dependency-visualization"></a>以代理程式為基礎的相依性視覺效果

相依性[視覺效果](concepts-dependency-visualization.md)可協助您將要評估和遷移的機器相依性視覺化。 對於以代理程式為基礎的視覺效果，下表摘要說明需求和限制


**需求** | **詳細資料**
--- | ---
**部署** | 部署相依性視覺效果之前，您應該先備妥 Azure Migrate 專案，並將 Azure Migrate： Server 評估工具加入至專案。 在設定 Azure Migrate 設備以探索您的內部部署機器之後，您會部署相依性視覺效果。<br/><br/> Azure Government 無法使用相依性視覺效果。
**服務對應** | 以代理程式為基礎的相依性視覺效果會使用[Azure 監視器記錄](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)中的[服務對應](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map)解決方案。<br/><br/> 若要部署，請將新的或現有的 Log Analytics 工作區與 Azure Migrate 專案產生關聯。
**Log Analytics 工作區** | 工作區必須與 Azure Migrate 專案位於相同的訂用帳戶中。<br/><br/> Azure Migrate 支援位於美國東部、東南亞和西歐區域的工作區。<br/><br/>  工作區必須位於[支援服務對應](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)的區域中。<br/><br/> 加入 Azure Migrate 專案的工作區之後，就無法修改它。
**費用** | 服務對應解決方案不會產生前180天的任何費用（從您建立 Log Analytics 工作區與 Azure Migrate 專案相關聯的那天開始計算）。<br/><br/> 經過 180 天後，會套用標準 Log Analytics 費用。<br/><br/> 在相關聯的 Log Analytics 工作區中使用服務對應以外的任何解決方案，將會產生標準 Log Analytics 費用。<br/><br/> 如果您刪除 Azure Migrate 專案，工作區不會隨之刪除。 刪除專案之後，服務對應不是免費的，而且每個節點都會依據 Log Analytics 工作區的付費層收費。
**代理程式** | 代理程式型相依性視覺效果需要在您要分析的每部機器上安裝兩個代理程式。<br/><br/> - [Microsoft Monitoring agent （MMA）](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)<br/><br/> - [Dependency agent](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent)。 
**網際網路連線** | 如果電腦未連線到網際網路，您需要在其上安裝 Log Analytics 閘道。


## <a name="next-steps"></a>後續步驟

[準備 Hyper-v VM 評估](tutorial-prepare-hyper-v.md)

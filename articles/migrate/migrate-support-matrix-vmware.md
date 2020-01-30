---
title: Azure Migrate 中的 VMware 評估支援
description: 深入瞭解 Azure Migrate 中的 VMware 評估支援。
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 8ed20ecd37eacdcb771db7c166ff8fc22b96cb89
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846187"
---
# <a name="support-matrix-for-vmware-assessment"></a>VMware 評估的支援矩陣 

本文摘要說明使用[Azure Migrate： Server 評估](migrate-services-overview.md#azure-migrate-server-migration-tool)來評估 VMware vm 的支援設定和限制。 如果您要尋找將 VMware Vm 遷移至 Azure 的相關資訊，請參閱[遷移支援矩陣](migrate-support-matrix-vmware-migration.md)。

## <a name="overview"></a>概觀

若要使用這篇文章來評估內部部署機器以遷移至 Azure，請將 Azure Migrate：伺服器評估工具新增至 Azure Migrate 專案。 您會部署[Azure Migrate 設備](migrate-appliance.md)。 設備會持續探索內部部署機器，並將設定和效能資料傳送至 Azure。 在機器探索之後，您會將探索到的機器收集到群組中，並執行群組的評量。


## <a name="limitations"></a>限制

**支援** | **詳細資料**
--- | ---
**評量限制**| 在單一[專案](migrate-support-matrix.md#azure-migrate-projects)中探索和評定最多35000個 VMware vm。
**專案限制** | 您可以在 Azure 訂用帳戶中建立多個專案。 專案可以包含 VMware Vm、Hyper-v Vm 和實體伺服器，最多可達評量限制。
**探索** | Azure Migrate 設備可以在 vCenter Server 上探索最多10000個 VMware Vm。
**評量** | 您最多可以在單一群組中新增35000部電腦。<br/><br/> 您可以在單一評估中評估多達35000個 Vm。

[深入瞭解](concepts-assessment-calculation.md)評量。


## <a name="application-discovery"></a>應用程式探索

除了探索機器以外，Azure Migrate：伺服器評估可以探索在機器上執行的應用程式、角色和功能。 探索您的應用程式清查可讓您找出並規劃專為您的內部部署工作負載量身打造的遷移路徑。 

**支援** | **詳細資料**
--- | ---
**探索** | 探索是無代理程式，使用電腦來賓認證，並使用 WMI 和 SSH 呼叫從遠端存取電腦。
**支援的機器** | 內部部署 VMware Vm。
**電腦作業系統** | 所有的 Windows 和 Linux 版本。
**vCenter 認證** | 具有唯讀存取權的 vCenter Server 帳戶，以及為虛擬機器 > 來賓作業啟用的許可權。
**VM 認證** | 目前支援所有 Windows 伺服器使用一個認證，而所有 Linux 伺服器都有一個認證。<br/><br/> 您會建立 Windows Vm 的來賓使用者帳戶，以及所有 Linux Vm 的一般/一般使用者帳戶（非 sudo 存取權）。
**VMware 工具** | VMware 工具必須安裝在您想要探索的 Vm 上並加以執行。
**埠存取** | 在執行您要探索之 Vm 的 ESXi 主機上，Azure Migrate 設備必須能夠連線到 TCP 通訊埠443。
**限制** | 針對應用程式探索，您最多可以探索每個設備10000。 

## <a name="vmware-requirements"></a>VMware 需求

**VMware** | **詳細資料**
--- | ---
**vCenter Server** | 您想要探索及評估的機器必須受 vCenter Server 5.5、6.0、6.5 或6.7 版的管理。
**許可權（評量）** | vCenter Server 唯讀帳戶。
**許可權（應用程式探索）** | 具有唯讀存取權的 vCenter Server 帳戶，以及為**虛擬機器啟用 > 來賓作業**的許可權。
**許可權（相依性視覺效果）** | 具有唯讀存取權的中央伺服器帳戶，以及為**虛擬機器**啟用的許可權 > **來賓作業**。


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate 設備需求

Azure Migrate 會使用[Azure Migrate 設備](migrate-appliance.md)來進行探索和評量。 適用于 VMware 的設備是使用 OVA 範本進行部署，並匯入 vCenter Server。 

- 瞭解 VMware 的[設備需求](migrate-appliance.md#appliance---vmware)。
- 瞭解設備需要存取的[url](migrate-appliance.md#url-access) 。

## <a name="port-access"></a>埠存取

**裝置** | **[連接]**
--- | ---
Appliance (設備) | TCP 通訊埠3389上的輸入連線，以允許應用裝置的遠端桌面連線。<br/><br/> 埠44368上的輸入連線，可使用 URL 從遠端存取應用裝置管理應用程式： ```https://<appliance-ip-or-name>:44368``` <br/><br/>埠443（HTTPS）、5671和5672（AMQP）上的輸出連線，以將探索和效能中繼資料傳送至 Azure Migrate。
vCenter 伺服器 | TCP 通訊埠443上的輸入連線，以允許設備收集設定和效能中繼資料以進行評量。 <br/><br/> 根據預設，設備會連線到埠443上的 vCenter。 如果 vCenter server 在不同的埠上接聽，您可以在設定探索時修改埠。
ESXi 主機 | **僅針對[應用程式探索](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#application-discovery)和[無代理](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-dependency-visualization)程式相依性視覺效果的必要項** <br/><br/> 設備會連線到 TCP 通訊埠443上的 ESXi 主機，以探索應用程式，並在主機上執行的 Vm 上執行無代理程式相依性視覺效果。

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


## <a name="agentless-dependency-visualization"></a>無代理程式的相依性視覺效果

這個選項目前為預覽狀態。 [深入了解](how-to-create-group-machine-dependencies-agentless.md)。 下表摘要說明需求。

**需求** | **詳細資料**
--- | ---
**部署** | 部署相依性視覺效果之前，您應該先備妥 Azure Migrate 專案，並將 Azure Migrate： Server 評估工具加入至專案。 在設定 Azure Migrate 設備以探索您的內部部署機器之後，您會部署相依性視覺效果。
**VM 支援** | 目前僅支援 VMware Vm。
**Windows VM** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64 位元)
**Linux VM** | Red Hat Enterprise Linux 7、6、5<br/> Ubuntu Linux 14.04、16.04<br/> Debian 7、8<br/> Oracle Linux 6、7<br/> CentOS 5、6、7。
**Windows 帳戶** |  視覺效果需要具有來賓存取權的使用者帳戶。
**Linux 帳戶** | 視覺效果需要具有根許可權的使用者帳戶。<br/><br/> 或者，使用者帳戶需要/bin/netstat 和/bin/ls 檔案的下列許可權： CAP_DAC_READ_SEARCH 和 CAP_SYS_PTRACE。
**VM 代理程式** | Vm 上不需要代理程式。
**VMware 工具** | VMware 工具必須安裝在您想要分析的 Vm 上並加以執行。
**vCenter 認證** | 具有唯讀存取權的 vCenter Server 帳戶，以及為虛擬機器 > 來賓作業啟用的許可權。
**埠存取** | 在執行您想要分析之 Vm 的 ESXi 主機上，Azure Migrate 設備必須能夠連線到 TCP 通訊埠443。



## <a name="next-steps"></a>後續步驟

- 請[參閱](best-practices-assessment.md)建立評量的最佳做法。
- [準備 VMware](tutorial-prepare-vmware.md)評估。

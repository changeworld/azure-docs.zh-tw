---
title: Azure Migrate 中的 VMware 評估支援
description: 瞭解使用 Azure Migrate Server 評估來支援 VMware VM 評估。
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: a0d05c56670c54aca25232a86b5a0e89d2f0bcfd
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983647"
---
# <a name="support-matrix-for-vmware-assessment"></a>VMware 評估的支援矩陣 

本文摘要說明當您使用[Azure Migrate： Server 評估](migrate-services-overview.md#azure-migrate-server-assessment-tool)工具來評估 VMware vm 以遷移至 Azure 時的必要條件和支援需求。 如果您想要將 VMware Vm 遷移至 Azure，請參閱[遷移支援矩陣](migrate-support-matrix-vmware-migration.md)。

若要評估 VMware Vm，請建立 Azure Migrate 專案，然後將伺服器評估工具加入至專案。 新增工具之後，您要部署[Azure Migrate 應用裝置](migrate-appliance.md)。 設備會持續探索內部部署機器，並將電腦中繼資料和效能資料傳送至 Azure。 探索完成之後，您會將探索到的機器收集到群組中，並執行群組的評量。

## <a name="limitations"></a>限制

**支援** | **詳細資料**
--- | ---
**專案限制** | 您可以在 Azure 訂用帳戶中建立多個專案。<br/><br/> 您可以在單一[專案](migrate-support-matrix.md#azure-migrate-projects)中探索及評估最多35000個 VMware vm。 專案也可以包含實體伺服器和 Hyper-v Vm，最多可達每個的評量限制。
**探索** | Azure Migrate 設備可以在 vCenter Server 上探索最多10000個 VMware Vm。
**評量** | 您最多可以在單一群組中新增35000部電腦。<br/><br/> 您可以在單一評估中評估多達35000個 Vm。

[深入瞭解](concepts-assessment-calculation.md)評量。


## <a name="application-discovery"></a>應用程式探索

除了探索機器以外，伺服器評估也可以探索在機器上執行的應用程式、角色和功能。 探索您的應用程式清查可讓您找出並規劃專為您的內部部署工作負載量身打造的遷移路徑。 

**支援** | **詳細資料**
--- | ---
**支援的機器** | 目前僅支援 VMware Vm 的應用程式探索。
**探索** | 應用程式探索是無代理程式。 它會使用電腦來賓認證，並使用 WMI 和 SSH 呼叫從遠端存取電腦。
**VM 支援** | 所有 Windows 和 Linux 版本都支援應用程式探索。
**vCenter 認證** | 應用程式探索需要具有唯讀存取權的 vCenter Server 帳戶，以及為虛擬機器 > 來賓作業啟用的許可權。
**VM 認證** | 應用程式探索目前支援所有 Windows 伺服器使用一個認證，而所有 Linux 伺服器都有一個認證。<br/><br/> 您會建立 Windows Vm 的來賓使用者帳戶，以及所有 Linux Vm 的一般/一般使用者帳戶（非 sudo 存取權）。
**VMware 工具** | VMware 工具必須安裝在您想要探索的 Vm 上並加以執行。 <br/> VMware 工具版本必須晚于10.2.0。
**PowerShell** | Vm 必須安裝 PowerShell 2.0 版或更新版本。
**埠存取** | 在執行您要探索之 Vm 的 ESXi 主機上，Azure Migrate 設備必須能夠連線到 TCP 通訊埠443。
**限制** | 針對應用程式探索，您可以在每個 Azure Migrate 設備上探索最多10000部 Vm。



## <a name="vmware-requirements"></a>VMware 需求

**VMware** | **詳細資料**
--- | ---
**VMware VM** | 所有的 Windows 和 Linux 作業系統都支援評量。
**vCenter Server** | 您想要探索及評估的機器必須受 vCenter Server 5.5、6.0、6.5 或6.7 版的管理。
**許可權（評量）** | vCenter Server 唯讀帳戶。
**許可權（應用程式探索）** | 具有唯讀存取權的 vCenter Server 帳戶，以及為**虛擬機器啟用 > 來賓作業**的許可權。
**許可權（相依性視覺效果）** | 具有唯讀存取權的 vCenter Server 帳戶，以及為**虛擬機器** > **來賓作業**啟用的許可權。


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate 設備需求

Azure Migrate 會使用[Azure Migrate 設備](migrate-appliance.md)來進行探索和評量。 您可以使用 OVA 範本將設備部署為 VMWare VM、匯入 vCenter Server，或使用[PowerShell 腳本](deploy-appliance-script.md)。

- 瞭解 VMware 的[設備需求](migrate-appliance.md#appliance---vmware)。
- 瞭解設備在[公用](migrate-appliance.md#public-cloud-urls)和[政府](migrate-appliance.md#government-cloud-urls)雲端中存取所需的 url。
- 在 Azure Government 中，您必須[使用腳本](deploy-appliance-script-government.md)來部署應用裝置。


## <a name="port-access"></a>埠存取

**裝置** | **建立**
--- | ---
Appliance (設備) | TCP 通訊埠3389上的輸入連線，以允許應用裝置的遠端桌面連線。<br/><br/> 埠44368上的輸入連線，可使用 URL 從遠端存取應用裝置管理應用程式：```https://<appliance-ip-or-name>:44368``` <br/><br/>埠443（HTTPS）上的輸出連線，以將探索和效能中繼資料傳送至 Azure Migrate。
vCenter 伺服器 | TCP 通訊埠443上的輸入連線，以允許設備收集設定和效能中繼資料以進行評量。 <br/><br/> 根據預設，設備會連線到埠443上的 vCenter。 如果 vCenter server 在不同的埠上接聽，您可以在設定探索時修改埠。
ESXi 主機（應用程式探索/無代理程式相依性分析） | 如果您想要執行[應用程式探索](how-to-discover-applications.md)或[無代理](concepts-dependency-visualization.md#agentless-analysis)程式相依性分析，則設備會連線到 TCP 通訊埠443上的 ESXi 主機，以探索應用程式，並在 vm 上執行無代理程式相依性視覺效果。

## <a name="application-discovery"></a>應用程式探索

除了探索機器以外，伺服器評估也可以探索在機器上執行的應用程式、角色和功能。 探索您的應用程式清查可讓您找出並規劃專為您的內部部署工作負載量身打造的遷移路徑。 

**支援** | **詳細資料**
--- | ---
**支援的機器** | 目前僅支援 VMware Vm 的應用程式探索。
**探索** | 應用程式探索是無代理程式。 它會使用電腦來賓認證，並使用 WMI 和 SSH 呼叫從遠端存取電腦。
**VM 支援** | 所有 Windows 和 Linux 版本都支援應用程式探索。
**vCenter 認證** | 應用程式探索需要具有唯讀存取權的 vCenter Server 帳戶，以及為虛擬機器 > 來賓作業啟用的許可權。
**VM 認證** | 應用程式探索目前支援所有 Windows 伺服器使用一個認證，而所有 Linux 伺服器都有一個認證。<br/><br/> 您會建立 Windows Vm 的來賓使用者帳戶，以及所有 Linux Vm 的一般/一般使用者帳戶（非 sudo 存取權）。
**VMware 工具** | VMware 工具必須安裝在您想要探索的 Vm 上並加以執行。 <br/> VMware 工具版本必須晚于10.2.0。
**PowerShell** | Vm 必須安裝 PowerShell 2.0 版或更新版本。
**埠存取** | 在執行您要探索之 Vm 的 ESXi 主機上，Azure Migrate 設備必須能夠連線到 TCP 通訊埠443。
**限制** | 針對應用程式探索，您可以在每個 Azure Migrate 設備上探索最多10000部 Vm。


## <a name="agentless-dependency-analysis-requirements"></a>無代理程式相依性分析需求

相依性[分析](concepts-dependency-visualization.md)可協助您識別您想要評估並遷移至 Azure 的內部部署機器之間的相依性。 下表摘要說明設定無代理程式相依性分析的需求。 

**需求** | **詳細資料**
--- | --- 
**部署之前** | 您應該已備妥 Azure Migrate 專案，並將伺服器評估工具加入至專案。<br/><br/>  您會在設定 Azure Migrate 設備以探索內部部署 VMWare 機器之後，部署相依性視覺效果。<br/><br/> [瞭解如何](create-manage-projects.md)第一次建立專案。<br/> [瞭解如何](how-to-assess.md)將評估工具加入至現有的專案。<br/> [瞭解如何](how-to-set-up-appliance-vmware.md)設定 Azure Migrate 應用裝置以進行 VMware vm 的評量。
**VM 支援** | 目前僅支援 VMware Vm。
**Windows VM** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 （64位）。
**Windows 帳戶** |  針對相依性分析，Azure Migrate 設備需要有網域系統管理員帳戶或本機系統管理員帳戶，才能存取 Windows Vm。
**Linux VM** | Red Hat Enterprise Linux 7、6、5<br/> Ubuntu Linux 14.04、16.04<br/> Debian 7、8<br/> Oracle Linux 6、7<br/> CentOS 5、6、7。
**Linux 帳戶** | 針對相依性分析，在 Linux 機器上，Azure Migrate 設備需要具有根許可權的使用者帳戶。<br/><br/> 或者，使用者帳戶需要/bin/netstat 和/bin/ls 檔案的下列許可權： CAP_DAC_READ_SEARCH 和 CAP_SYS_PTRACE。
**必要的代理程式** | 您想要分析的電腦上不需要代理程式。
**VMware Tools** | 您必須在想要分析的每個 VM 上安裝並執行 VMware 工具（10.2 以後版本）。
**vCenter Server 認證** | 相依性視覺效果需要具有唯讀存取權的 vCenter Server 帳戶，以及為虛擬機器 > 來賓作業啟用的許可權。 
**PowerShell** | Vm 必須安裝 PowerShell 2.0 版或更新版本。
**埠存取** | 在執行您想要分析之 Vm 的 ESXi 主機上，Azure Migrate 設備必須能夠連線到 TCP 通訊埠443。


## <a name="agent-based-dependency-analysis-requirements"></a>以代理程式為基礎的相依性分析需求

相依性[分析](concepts-dependency-visualization.md)可協助您識別您想要評估並遷移至 Azure 的內部部署機器之間的相依性。 下表摘要說明設定以代理程式為基礎之相依性分析的需求。 

**需求** | **詳細資料** 
--- | --- 
**部署之前** | 您應該已備妥 Azure Migrate 專案，並將 Azure Migrate： Server 評估工具加入至專案。<br/><br/>  您在設定 Azure Migrate 設備以探索內部部署機器之後，部署相依性視覺效果<br/><br/> [瞭解如何](create-manage-projects.md)第一次建立專案。<br/> [瞭解如何](how-to-assess.md)將評估工具加入至現有的專案。<br/> 瞭解如何設定 Azure Migrate 應用裝置，以進行[hyper-v](how-to-set-up-appliance-hyper-v.md)、 [VMware](how-to-set-up-appliance-vmware.md)或實體伺服器的評量。
**Azure Government** | Azure Government 無法使用相依性視覺效果。
**Log Analytics** | Azure Migrate 使用[Azure 監視器記錄](../log-analytics/log-analytics-overview.md)中的[服務對應](../operations-management-suite/operations-management-suite-service-map.md)解決方案來進行相依性視覺效果。<br/><br/> 將新的或現有的 Log Analytics 工作區與 Azure Migrate 專案建立關聯。 加入 Azure Migrate 專案的工作區之後，就無法修改它。 <br/><br/> 工作區必須與 Azure Migrate 專案位於相同的訂用帳戶中。<br/><br/> 工作區必須位於 [美國東部]、[東南亞] 或 [西歐] 區域。 其他區域中的工作區無法與專案相關聯。<br/><br/> 工作區必須位於[支援服務對應](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)的區域中。<br/><br/> 在 Log Analytics 中，與 Azure Migrate 相關聯的工作區會以遷移專案金鑰和專案名稱標記。
**必要的代理程式** | 在您要分析的每部電腦上，安裝下列代理程式：<br/><br/> [Microsoft Monitoring agent （MMA）](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)。<br/> [Dependency agent](../azure-monitor/platform/agents-overview.md#dependency-agent)。<br/><br/> 如果內部部署機器未連線到網際網路，您需要在其上下載並安裝 Log Analytics 閘道。<br/><br/> 深入瞭解如何安裝[Dependency agent](how-to-create-group-machine-dependencies.md#install-the-dependency-agent)和[MMA](how-to-create-group-machine-dependencies.md#install-the-mma)。
**Log Analytics 工作區** | 工作區必須與 Azure Migrate 專案位於相同的訂用帳戶中。<br/><br/> Azure Migrate 支援位於美國東部、東南亞及西歐區域的工作區。<br/><br/>  工作區必須位於[支援服務對應](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)的區域中。<br/><br/> 加入 Azure Migrate 專案的工作區之後，就無法修改它。
**損失** | 服務對應解決方案不會產生前180天的任何費用（從您將 Log Analytics 工作區關聯至 Azure Migrate 專案的那一天）/<br/><br/> 經過 180 天後，會套用標準 Log Analytics 費用。<br/><br/> 在相關聯的 Log Analytics 工作區中使用服務對應以外的任何解決方案，將會產生 Log Analytics 的[標準費用](https://azure.microsoft.com/pricing/details/log-analytics/)。<br/><br/> 刪除 Azure Migrate 專案時，工作區不會隨之刪除。 刪除專案之後，服務對應的使用量不是免費的，而且每個節點都會依據 Log Analytics 工作區的付費層收費<br/><br/>如果您的專案是在 Azure Migrate 正式運作（GA-28 2018 年2月）之前建立的，則可能會產生額外的服務對應費用。 為確保只在180天后付款，我們建議您建立新的專案，因為 GA 之前的現有工作區仍可計費。
**管理** | 當您向工作區註冊代理程式時，您會使用 Azure Migrate 專案所提供的識別碼和金鑰。<br/><br/> 您可以使用 Azure Migrate 外部的 Log Analytics 工作區。<br/><br/> 如果您刪除相關聯的 Azure Migrate 專案，則不會自動刪除工作區。 請[手動將其刪除](../azure-monitor/platform/manage-access.md)。<br/><br/> 除非您刪除 Azure Migrate 專案，否則請勿刪除 Azure Migrate 建立的工作區。 如果這樣做，相依性視覺效果功能不會如預期般運作。
**網際網路連線能力** | 如果電腦未連線到網際網路，您需要在其上安裝 Log Analytics 閘道。
**Azure Government** | 不支援以代理程式為基礎的相依性分析。


## <a name="next-steps"></a>後續步驟

- 請[參閱](best-practices-assessment.md)建立評量的最佳做法。
- [準備 VMware](tutorial-prepare-vmware.md)評估。

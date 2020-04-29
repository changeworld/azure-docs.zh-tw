---
title: 支援 Azure Migrate 中的 Hyper-v 評估
description: 瞭解 Azure Migrate Server 評估的 Hyper-v 評估支援
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 990d5026d9621c144c31635fabac4416eb9d20e6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81538166"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Hyper-v 評估的支援矩陣

本文摘要說明當您使用[Azure Migrate： Server 評估](migrate-services-overview.md#azure-migrate-server-assessment-tool)工具來評估要遷移至 Azure 的 hyper-v vm 時的必要條件和支援需求。 如果您想要將 Hyper-v Vm 遷移至 Azure，請參閱[遷移支援矩陣](migrate-support-matrix-hyper-v-migration.md)。

若要設定 Hyper-v VM 評估，請建立 Azure Migrate 專案，並將伺服器評估工具新增至專案。 新增工具之後，您要部署[Azure Migrate 應用裝置](migrate-appliance.md)。 設備會持續探索內部部署機器，並將電腦中繼資料和效能資料傳送至 Azure。 探索完成之後，您會將探索到的機器收集到群組中，並執行群組的評量。


## <a name="limitations"></a>限制

**支援** | **詳細資料**
--- | ---
**評量限制** | 您可以在單一[Azure Migrate 專案](migrate-support-matrix.md#azure-migrate-projects)中探索及評估最多35000個 hyper-v vm。
**專案限制** | 您可以在 Azure 訂用帳戶中建立多個專案。 除了 Hyper-v Vm 之外，專案還可以包含 VMware Vm 和實體伺服器，最多可達每個虛擬機器的評量限制。
**探索** | Azure Migrate 設備可以探索最多5000部 Hyper-v Vm。<br/><br/> 設備最多可以連接到300的 Hyper-v 主機。
**評量** | 您最多可以在單一群組中新增35000部電腦。<br/><br/> 針對群組，您可以在單一評估中評估最多35000個 Vm。

[深入瞭解](concepts-assessment-calculation.md)評量。



## <a name="hyper-v-host-requirements"></a>Hyper-v 主機需求

| **支援**                | **詳細資料**               
| :-------------------       | :------------------- |
| **Hyper-V 主機**       | Hyper-v 主機可以是獨立的，或部署在叢集中。<br/><br/> Hyper-v 主機可以執行 Windows Server 2019、Windows Server 2016 或 Windows Server 2012 R2。<br/> 您無法評估位於執行 Windows Server 2012 的 Hyper-V 主機上的 VM。
| **權限**           | 您需要 Hyper-v 主機的系統管理員許可權。 <br/> 如果您不想要指派系統管理員許可權，請建立本機或網域使用者帳戶，並將使用者帳戶新增至這些群組-遠端系統管理使用者、Hyper-v 系統管理員和效能監視器使用者。 |
| **PowerShell 遠端**   | 必須在每部 Hyper-v 主機上啟用[PowerShell 遠端](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/enable-psremoting?view=powershell-7)。 |
| **Hyper-V 複本**       | 如果您使用 Hyper-v 複本（或您有多個 vm 具有相同的 VM 識別碼），而且您使用 Azure Migrate 探索原始和複寫的 Vm，則 Azure Migrate 所產生的評量可能不正確。 |


## <a name="hyper-v-vm-requirements"></a>Hyper-v VM 需求

| **支援**                  | **詳細資料**               
| :----------------------------- | :------------------- |
| **作業系統** | 所有的[Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)和[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)作業系統。 |
| **Integration Services**       | [Hyper-v Integration Services](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services)必須在您評估的 vm 上執行，才能捕捉作業系統資訊。 |


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate 設備需求

Azure Migrate 會使用[Azure Migrate 設備](migrate-appliance.md)來進行探索和評量。 您可以使用您從入口網站下載的壓縮 Hyper-v VHD，或使用[PowerShell 腳本](deploy-appliance-script.md)，來部署應用裝置。

- 瞭解 Hyper-v 的[設備需求](migrate-appliance.md#appliance---hyper-v)。
- 瞭解設備在[公用](migrate-appliance.md#public-cloud-urls)和[政府](migrate-appliance.md#government-cloud-urls)雲端中存取所需的 url。
- 在 Azure Government 中，您必須使用腳本來部署應用裝置。

## <a name="port-access"></a>埠存取

下表摘要說明評量的通訊埠需求。

**裝置** | **建立**
--- | ---
**台** | TCP 通訊埠3389上的輸入連線，以允許應用裝置的遠端桌面連線。<br/><br/> 埠44368上的輸入連線，可使用 URL 從遠端存取應用裝置管理應用程式：``` https://<appliance-ip-or-name>:44368 ```<br/><br/> 埠443（HTTPS）上的輸出連線，以將探索和效能中繼資料傳送至 Azure Migrate。
**Hyper-v 主機/叢集** | WinRM 埠5985（HTTP）和5986（HTTPS）上的輸入連線，可使用通用訊息模型（CIM）會話來提取 Hyper-v Vm 的中繼資料和效能資料。

## <a name="agent-based-dependency-analysis-requirements"></a>以代理程式為基礎的相依性分析需求

相依性[分析](concepts-dependency-visualization.md)可協助您識別您想要評估並遷移至 Azure 的內部部署機器之間的相依性。 下表摘要說明設定以代理程式為基礎之相依性分析的需求。 Hyper-v 目前僅支援以代理程式為基礎的相依性視覺效果。 

**需求** | **詳細資料** 
--- | --- 
**部署之前** | 您應該已備妥 Azure Migrate 專案，並將伺服器評估工具加入至專案。<br/><br/>  您在設定 Azure Migrate 設備以探索內部部署機器之後，部署相依性視覺效果<br/><br/> [瞭解如何](create-manage-projects.md)第一次建立專案。<br/> [瞭解如何](how-to-assess.md)將評估工具加入至現有的專案。<br/> 瞭解如何設定 Azure Migrate 應用裝置，以評估[Hyper-v vm](how-to-set-up-appliance-hyper-v.md)。
**Azure Government** | Azure Government 無法使用相依性視覺效果。
**Log Analytics** | Azure Migrate 使用[Azure 監視器記錄](../log-analytics/log-analytics-overview.md)中的[服務對應](../operations-management-suite/operations-management-suite-service-map.md)解決方案來進行相依性視覺效果。<br/><br/> 將新的或現有的 Log Analytics 工作區與 Azure Migrate 專案建立關聯。 加入 Azure Migrate 專案的工作區之後，就無法修改它。 <br/><br/> 工作區必須與 Azure Migrate 專案位於相同的訂用帳戶中。<br/><br/> 工作區必須位於 [美國東部]、[東南亞] 或 [西歐] 區域。 其他區域中的工作區無法與專案相關聯。<br/><br/> 工作區必須位於[支援服務對應](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)的區域中。<br/><br/> 在 Log Analytics 中，與 Azure Migrate 相關聯的工作區會以遷移專案金鑰和專案名稱標記。
**必要的代理程式** | 在您要分析的每部電腦上，安裝下列代理程式：<br/><br/> [Microsoft Monitoring agent （MMA）](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)。<br/> [Dependency agent](../azure-monitor/platform/agents-overview.md#dependency-agent)。<br/><br/> 如果內部部署機器未連線到網際網路，您需要在其上下載並安裝 Log Analytics 閘道。<br/><br/> 深入瞭解如何安裝[Dependency agent](how-to-create-group-machine-dependencies.md#install-the-dependency-agent)和[MMA](how-to-create-group-machine-dependencies.md#install-the-mma)。
**Log Analytics 工作區** | 工作區必須與 Azure Migrate 專案位於相同的訂用帳戶中。<br/><br/> Azure Migrate 支援位於美國東部、東南亞及西歐區域的工作區。<br/><br/>  工作區必須位於[支援服務對應](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)的區域中。<br/><br/> 加入 Azure Migrate 專案的工作區之後，就無法修改它。
**損失** | 服務對應解決方案不會產生前180天的任何費用（從您將 Log Analytics 工作區關聯至 Azure Migrate 專案的那一天）/<br/><br/> 經過 180 天後，會套用標準 Log Analytics 費用。<br/><br/> 在相關聯的 Log Analytics 工作區中使用服務對應以外的任何解決方案，將會產生 Log Analytics 的[標準費用](https://azure.microsoft.com/pricing/details/log-analytics/)。<br/><br/> 刪除 Azure Migrate 專案時，工作區不會隨之刪除。 刪除專案之後，服務對應的使用量不是免費的，而且每個節點都會依據 Log Analytics 工作區的付費層收費<br/><br/>如果您的專案是在 Azure Migrate 正式運作（GA-28 2018 年2月）之前建立的，則可能會產生額外的服務對應費用。 為確保只在180天后付款，我們建議您建立新的專案，因為 GA 之前的現有工作區仍可計費。
**管理** | 當您向工作區註冊代理程式時，您會使用 Azure Migrate 專案所提供的識別碼和金鑰。<br/><br/> 您可以使用 Azure Migrate 外部的 Log Analytics 工作區。<br/><br/> 如果您刪除相關聯的 Azure Migrate 專案，則不會自動刪除工作區。 請[手動將其刪除](../azure-monitor/platform/manage-access.md)。<br/><br/> 除非您刪除 Azure Migrate 專案，否則請勿刪除 Azure Migrate 建立的工作區。 如果這樣做，相依性視覺效果功能不會如預期般運作。
**網際網路連線能力** | 如果電腦未連線到網際網路，您需要在其上安裝 Log Analytics 閘道。
**Azure Government** | 不支援以代理程式為基礎的相依性分析。

## <a name="next-steps"></a>後續步驟

[準備 Hyper-v VM 評估](tutorial-prepare-hyper-v.md)

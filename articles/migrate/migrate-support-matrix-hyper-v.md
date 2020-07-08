---
title: 支援 Azure Migrate 中的 Hyper-v 評估
description: 瞭解 Azure Migrate Server 評估的 Hyper-v 評估支援
ms.topic: conceptual
ms.date: 06/14/2020
ms.openlocfilehash: c2df23b1566b4d793f4511499fd82d7912d0520f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85051912"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Hyper-v 評估的支援矩陣

本文摘要說明當您使用[Azure Migrate： Server 評估](migrate-services-overview.md#azure-migrate-server-assessment-tool)工具來評估要遷移至 Azure 的 hyper-v vm 時的必要條件和支援需求。 如果您想要將 Hyper-v Vm 遷移至 Azure，請參閱[遷移支援矩陣](migrate-support-matrix-hyper-v-migration.md)。

若要設定 Hyper-v VM 評估，請建立 Azure Migrate 專案，並將伺服器評估工具新增至專案。 新增工具之後，請部署 [Azure Migrate 設備](migrate-appliance.md)。 設備會持續探索內部部署機器，並將機器的中繼資料和效能資料傳送至 Azure。 探索完成之後，請將探索到的機器收集到群組中，然後為群組執行評估。


## <a name="limitations"></a>限制

**支援** | **詳細資料**
--- | ---
**評量限制** | 您可以在單一[Azure Migrate 專案](migrate-support-matrix.md#azure-migrate-projects)中探索及評估最多35000個 hyper-v vm。
**專案限制** | 您可以在 Azure 訂用帳戶中建立多個專案。 除了 Hyper-v Vm 之外，專案還可以包含 VMware Vm 和實體伺服器，最多可達每個虛擬機器的評量限制。
**探索** | Azure Migrate 設備可以探索最多5000部 Hyper-v Vm。<br/><br/> 設備最多可以連接到300的 Hyper-v 主機。
**評量** | 您最多可以在單一群組中新增 35,000 部機器。<br/><br/> 針對群組，您可以在單一評估中評估最多35000個 Vm。

[深入了解](concepts-assessment-calculation.md)評估。



## <a name="hyper-v-host-requirements"></a>Hyper-v 主機需求

| **支援**                | **詳細資料**               
| :-------------------       | :------------------- |
| **Hyper-V 主機**       | Hyper-v 主機可以是獨立的，或部署在叢集中。<br/><br/> Hyper-v 主機可以執行 Windows Server 2019、Windows Server 2016 或 Windows Server 2012 R2。<br/> 您無法評估位於執行 Windows Server 2012 的 Hyper-V 主機上的 VM。
| **權限**           | 您需要 Hyper-v 主機的系統管理員許可權。 <br/> 如果您不想要指派系統管理員許可權，請建立本機或網域使用者帳戶，並將使用者帳戶新增至這些群組-遠端系統管理使用者、Hyper-v 系統管理員和效能監視器使用者。 |
| **PowerShell 遠端**   | 必須在每部 Hyper-v 主機上啟用[PowerShell 遠端](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/enable-psremoting?view=powershell-7)。 |
| **Hyper-V 複本**       | 如果您使用 Hyper-v 複本（或您有多個 vm 具有相同的 VM 識別碼），而且您使用 Azure Migrate 探索原始和複寫的 Vm，則 Azure Migrate 所產生的評量可能不正確。 |


## <a name="vm-requirements"></a>VM 需求

| **支援**                  | **詳細資料**               
| :----------------------------- | :------------------- |
| **作業系統** | 所有作業系統都可以進行評估以進行遷移。  |
| **Integration Services**       | [Hyper-v Integration Services](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services)必須在您評估的 vm 上執行，才能捕捉作業系統資訊。 |


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate 設備需求

Azure Migrate 會使用 [Azure Migrate 設備](migrate-appliance.md)來進行探索和評估。 您可以使用您從入口網站下載的壓縮 Hyper-v VHD，或使用[PowerShell 腳本](deploy-appliance-script.md)，來部署應用裝置。

- 瞭解 Hyper-v 的[設備需求](migrate-appliance.md#appliance---hyper-v)。
- 了解設備必須在[公用](migrate-appliance.md#public-cloud-urls)和[政府](migrate-appliance.md#government-cloud-urls)雲端中存取的 URL。
- 在 Azure Government 中，您必須[使用指令碼](deploy-appliance-script-government.md)來部署設備。

## <a name="port-access"></a>連接埠存取

下表摘要說明評量的通訊埠需求。

**裝置** | **[連接]**
--- | ---
**設備** | TCP 連接埠 3389上的輸入連線，以允許遠端桌面對設備連線。<br/><br/> 連接埠 44368 上的輸入連線，以使用 URL 從遠端存取設備管理應用程式：``` https://<appliance-ip-or-name>:44368 ```<br/><br/> 埠443（HTTPS）上的輸出連線，以將探索和效能中繼資料傳送至 Azure Migrate。
**Hyper-v 主機/叢集** | WinRM 埠5985（HTTP）上的輸入連線，可使用通用訊息模型（CIM）會話提取 Hyper-v Vm 的中繼資料和效能資料。

## <a name="agent-based-dependency-analysis-requirements"></a>代理程式型相依性分析需求

[相依性分析](concepts-dependency-visualization.md)可協助您識別所要評估並遷移至 Azure 的內部部署機器之間的相依性。 下表摘要說明用於設定代理程式型相依性分析的需求。 Hyper-v 目前僅支援以代理程式為基礎的相依性視覺效果。 

**需求** | **詳細資料** 
--- | --- 
**部署之前** | 請先備妥 Azure Migrate 專案，並已在專案中新增伺服器評估工具。<br/><br/>  請在設定 Azure Migrate 設備以探索內部部署機器之後，部署相依性視覺效果。<br/><br/> [了解如何](create-manage-projects.md)第一次建立專案。<br/> [了解如何](how-to-assess.md)將評估工具新增至現有專案。<br/> 瞭解如何設定 Azure Migrate 應用裝置，以評估[Hyper-v vm](how-to-set-up-appliance-hyper-v.md)。
**Azure Government** | 在 Azure Government 中無法使用相依性視覺效果。
**Log Analytics** | Azure Migrate 會使用 [Azure 監視器記錄](../log-analytics/log-analytics-overview.md)中的[服務對應](../operations-management-suite/operations-management-suite-service-map.md)解決方案來實現相依性視覺效果。<br/><br/> 請將新的或現有的 Log Analytics 工作區與 Azure Migrate 專案建立關聯。 Azure Migrate 專案的工作區在新增之後就無法進行修改。 <br/><br/> 此工作區必須位於和 Azure Migrate 專案相同的訂用帳戶中。<br/><br/> 此工作區必須位於「美國東部」、「東南亞」或「西歐」區域。 其他區域中的工作區則無法與專案相關聯。<br/><br/> 此工作區必須位於[支援服務對應](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)的區域中。<br/><br/> 在 Log Analytics 中，與 Azure Migrate 相關聯的工作區會標記上遷移專案金鑰和專案名稱。
**所需的代理程式** | 在您要分析的每部機器上安裝下列代理程式：<br/><br/> [Microsoft Monitoring agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)。<br/> [相依性代理程式](../azure-monitor/platform/agents-overview.md#dependency-agent)。<br/><br/> 如果內部部署機器未連線到網際網路，則需要下載 Log Analytics 閘道並安裝到機器上。<br/><br/> 深入了解如何安裝 [Dependency Agent](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) 和 [MMA](how-to-create-group-machine-dependencies.md#install-the-mma)。
**Log Analytics 工作區** | 此工作區必須位於和 Azure Migrate 專案相同的訂用帳戶中。<br/><br/> Azure Migrate 可支援位於美國東部、東南亞和西歐區域的工作區。<br/><br/>  此工作區必須位於[支援服務對應](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)的區域中。<br/><br/> Azure Migrate 專案的工作區在新增之後就無法進行修改。
**成本** | 服務對應解決方案在使用的前 180 天 (從您將 Log Analytics 工作區關聯至 Azure Migrate 專案的那一天算起) 不會產生任何費用。<br/><br/> 經過 180 天後，會套用標準 Log Analytics 費用。<br/><br/> 在相關聯的 Log Analytics 工作區中使用非服務對應的解決方案則會產生 Log Analytics 的[標準費用](https://azure.microsoft.com/pricing/details/log-analytics/)。<br/><br/> 刪除 Azure Migrate 專案時，工作區不會隨之刪除。 在專案刪除後，服務對應就無法免費使用，系統會根據 Log Analytics 工作區的付費層向每個節點收取費用。<br/><br/>如果您的專案是在 Azure Migrate 正式發行 (GA - 2018 年 2 月 28 日) 之前建立的，則可能會產生額外的服務對應費用。 為確保在 180 天後才要付費，建議您建立新的專案，因為 GA 之前的既有工作區仍要付費。
**管理** | 當您向工作區註冊代理程式時，請使用 Azure Migrate 專案所提供的識別碼和金鑰。<br/><br/> 您可以使用 Azure Migrate 外部的 Log Analytics 工作區。<br/><br/> 如果您刪除相關聯的 Azure Migrate 專案，工作區並不會自動刪除。 [請手動將其刪除](../azure-monitor/platform/manage-access.md)。<br/><br/> 除非您刪除 Azure Migrate 專案，否則請勿刪除 Azure Migrate 建立的工作區。 如果這樣做，相依性視覺效果功能不會如預期般運作。
**網際網路連線** | 如果機器未連線到網際網路，則需要在機器上安裝 Log Analytics 閘道。
**Azure Government** | 不支援代理程式型的相依性分析。

## <a name="next-steps"></a>後續步驟

[準備 Hyper-v VM 評估](tutorial-prepare-hyper-v.md)

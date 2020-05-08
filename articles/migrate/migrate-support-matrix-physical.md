---
title: Azure Migrate 中的實體伺服器評量支援
description: 瞭解 Azure Migrate Server 評估的實體伺服器評量支援
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 31fd676a339a6c82cec84e0f355ac875f68a653c
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983664"
---
# <a name="support-matrix-for-physical-server-assessment"></a>實體伺服器評估的支援矩陣 

本文摘要說明當您使用[Azure Migrate： Server 評估](migrate-services-overview.md#azure-migrate-server-assessment-tool)工具來評估要遷移至 Azure 的實體伺服器時的必要條件和支援需求。 如果您想要將實體伺服器遷移至 Azure，請參閱[遷移支援矩陣](migrate-support-matrix-physical-migration.md)。


若要評估實體伺服器，您可以建立 Azure Migrate 專案，並將伺服器評估工具加入至專案。 新增工具之後，您要部署[Azure Migrate 應用裝置](migrate-appliance.md)。 設備會持續探索內部部署機器，並將電腦中繼資料和效能資料傳送至 Azure。 探索完成之後，您會將探索到的機器收集到群組中，並執行群組的評量。


## <a name="limitations"></a>限制

**支援** | **詳細資料**
--- | ---
**評量限制** | 您可以在單一[Azure Migrate 專案](migrate-support-matrix.md#azure-migrate-projects)中探索及評估最多35000部實體伺服器。
**專案限制** | 您可以在 Azure 訂用帳戶中建立多個專案。 除了實體伺服器之外，專案還可以包含 VMware Vm 和 Hyper-v Vm，最多可達每個虛擬機器的評量限制。
**探索** | Azure Migrate 設備可以探索最多250部實體伺服器。
**評量** | 您最多可以在單一群組中新增35000部電腦。<br/><br/> 您可以在單一評估中評估多達35000部機器。

[深入瞭解](concepts-assessment-calculation.md)評量。

## <a name="physical-server-requirements"></a>實體伺服器需求

| **支援**                | **詳細資料**               
| :-------------------       | :------------------- |
| **實體伺服器部署**       | 實體伺服器可以是獨立的，或部署在叢集中。 |
| **權限**           | **Windows：** 您必須是網域系統管理員，或您想要探索的所有 Windows 伺服器上的本機系統管理員。 使用者帳戶應新增至下列群組：遠端系統管理使用者、效能監視器使用者和效能記錄使用者。 <br/><br/> **Linux：** 您在要探索的 Linux 伺服器上必須要有根帳戶。 |
| **作業系統** | Azure 支援的所有[windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)和[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)伺服器作業系統，但 WINDOWS Server 2003 和 SUSE Linux 除外。<br/><br/> Windows 10 和 Windows 8 用戶端作業系統。 |


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate 設備需求

Azure Migrate 會使用[Azure Migrate 設備](migrate-appliance.md)來進行探索和評量。 實體伺服器的設備可以在 VM 或實體機器上執行。 

- 瞭解實體伺服器的[設備需求](migrate-appliance.md#appliance---physical)。
- 瞭解設備在[公用](migrate-appliance.md#public-cloud-urls)和[政府](migrate-appliance.md#government-cloud-urls)雲端中存取所需的 url。
- 您可以使用從 Azure 入口網站下載的[PowerShell 腳本](how-to-set-up-appliance-physical.md)來設定設備。
在 Azure Government 中，[使用此腳本](deploy-appliance-script-government.md)部署應用裝置。

## <a name="port-access"></a>埠存取

下表摘要說明評量的通訊埠需求。

**裝置** | **建立**
--- | ---
**台** | TCP 通訊埠3389上的輸入連線，以允許遠端桌面連線到應用裝置。<br/><br/> 埠44368上的輸入連線，可使用 URL 從遠端存取應用裝置管理應用程式：``` https://<appliance-ip-or-name>:44368 ```<br/><br/> 埠443（HTTPS）上的輸出連線，以將探索和效能中繼資料傳送至 Azure Migrate。
**實體伺服器** | **Windows：** WinRM 埠5985（HTTP）和5986（HTTPS）上的輸入連線，以從 Windows 伺服器提取設定和效能中繼資料。 <br/><br/> **Linux：** 埠22（UDP）上的輸入連線，用來從 Linux 伺服器提取設定和效能中繼資料。 |

## <a name="agent-based-dependency-analysis-requirements"></a>以代理程式為基礎的相依性分析需求

相依性[分析](concepts-dependency-visualization.md)可協助您識別您想要評估並遷移至 Azure 的內部部署機器之間的相依性。 下表摘要說明設定以代理程式為基礎之相依性分析的需求。 實體伺服器目前僅支援以代理程式為基礎的相依性分析。

**需求** | **詳細資料** 
--- | --- 
**部署之前** | 您應該已備妥 Azure Migrate 專案，並將伺服器評估工具加入至專案。<br/><br/>  您在設定 Azure Migrate 設備以探索內部部署機器之後，部署相依性視覺效果<br/><br/> [瞭解如何](create-manage-projects.md)第一次建立專案。<br/> [瞭解如何](how-to-assess.md)將評估工具加入至現有的專案。<br/> 瞭解如何設定 Azure Migrate 應用裝置，以進行[hyper-v](how-to-set-up-appliance-hyper-v.md)、 [VMware](how-to-set-up-appliance-vmware.md)或實體伺服器的評量。
**Azure Government** | Azure Government 無法使用相依性視覺效果。
**Log Analytics** | Azure Migrate 使用[Azure 監視器記錄](../log-analytics/log-analytics-overview.md)中的[服務對應](../operations-management-suite/operations-management-suite-service-map.md)解決方案來進行相依性視覺效果。<br/><br/> 將新的或現有的 Log Analytics 工作區與 Azure Migrate 專案建立關聯。 加入 Azure Migrate 專案的工作區之後，就無法修改它。 <br/><br/> 工作區必須與 Azure Migrate 專案位於相同的訂用帳戶中。<br/><br/> 工作區必須位於 [美國東部]、[東南亞] 或 [西歐] 區域。 其他區域中的工作區無法與專案相關聯。<br/><br/> 工作區必須位於[支援服務對應](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)的區域中。<br/><br/> 在 Log Analytics 中，與 Azure Migrate 相關聯的工作區會以遷移專案金鑰和專案名稱標記。
**必要的代理程式** | 在您要分析的每部電腦上，安裝下列代理程式：<br/><br/> [Microsoft Monitoring agent （MMA）](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)。<br/> [Dependency agent](../azure-monitor/platform/agents-overview.md#dependency-agent)。<br/><br/> 如果內部部署機器未連線到網際網路，您需要在其上下載並安裝 Log Analytics 閘道。<br/><br/> 深入瞭解如何安裝[Dependency agent](how-to-create-group-machine-dependencies.md#install-the-dependency-agent)和[MMA](how-to-create-group-machine-dependencies.md#install-the-mma)。
**Log Analytics 工作區** | 工作區必須與 Azure Migrate 專案位於相同的訂用帳戶中。<br/><br/> Azure Migrate 支援位於美國東部、東南亞及西歐區域的工作區。<br/><br/>  工作區必須位於[支援服務對應](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)的區域中。<br/><br/> 加入 Azure Migrate 專案的工作區之後，就無法修改它。
**損失** | 服務對應解決方案不會產生前180天的任何費用（從您將 Log Analytics 工作區關聯至 Azure Migrate 專案的那一天）/<br/><br/> 經過 180 天後，會套用標準 Log Analytics 費用。<br/><br/> 在相關聯的 Log Analytics 工作區中使用服務對應以外的任何解決方案，將會產生 Log Analytics 的[標準費用](https://azure.microsoft.com/pricing/details/log-analytics/)。<br/><br/> 刪除 Azure Migrate 專案時，工作區不會隨之刪除。 刪除專案之後，服務對應的使用量不是免費的，而且每個節點都會依據 Log Analytics 工作區的付費層收費<br/><br/>如果您的專案是在 Azure Migrate 正式運作（GA-28 2018 年2月）之前建立的，則可能會產生額外的服務對應費用。 為確保只在180天后付款，我們建議您建立新的專案，因為 GA 之前的現有工作區仍可計費。
**管理** | 當您向工作區註冊代理程式時，您會使用 Azure Migrate 專案所提供的識別碼和金鑰。<br/><br/> 您可以使用 Azure Migrate 外部的 Log Analytics 工作區。<br/><br/> 如果您刪除相關聯的 Azure Migrate 專案，則不會自動刪除工作區。 請[手動將其刪除](../azure-monitor/platform/manage-access.md)。<br/><br/> 除非您刪除 Azure Migrate 專案，否則請勿刪除 Azure Migrate 建立的工作區。 如果這樣做，相依性視覺效果功能不會如預期般運作。
**網際網路連線能力** | 如果電腦未連線到網際網路，您需要在其上安裝 Log Analytics 閘道。
**Azure Government** | 不支援以代理程式為基礎的相依性分析。

## <a name="next-steps"></a>後續步驟

[準備進行實體伺服器評估](tutorial-prepare-physical.md)。

---
title: Azure Migrate 中的實體伺服器評量支援
description: 瞭解 Azure Migrate Server 評定的實體伺服器評量支援
ms.topic: conceptual
ms.date: 06/03/2020
ms.openlocfilehash: dffa95fe717f8588f56b9dee60ede8bbf44aceb9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89660343"
---
# <a name="support-matrix-for-physical-server-assessment"></a>實體伺服器評量的支援矩陣 

本文摘要說明當您使用 [Azure Migrate：伺服器評估](migrate-services-overview.md#azure-migrate-server-assessment-tool) 工具來評估要遷移至 Azure 的實體伺服器時的必要條件和支援需求。 如果您想要將實體伺服器遷移至 Azure，請參閱 [遷移支援矩陣](migrate-support-matrix-physical-migration.md)。


若要評估實體伺服器，請建立 Azure Migrate 專案，並將伺服器評定工具新增至專案。 新增工具之後，請部署 [Azure Migrate 設備](migrate-appliance.md)。 設備會持續探索內部部署機器，並將機器的中繼資料和效能資料傳送至 Azure。 探索完成之後，請將探索到的機器收集到群組中，然後為群組執行評估。


## <a name="limitations"></a>限制

**支援** | **詳細資料**
--- | ---
**評量限制** | 您可以在單一 [Azure Migrate 專案](migrate-support-matrix.md#azure-migrate-projects)中探索及評定最多35000個實體伺服器。
**專案限制** | 您可以在 Azure 訂用帳戶中建立多個專案。 除了實體伺服器之外，專案也可以包含 VMware Vm 和 Hyper-v Vm，最多可達每個 vm 的評量限制。
**探索** | Azure Migrate 設備最多可探索1000部實體伺服器。
**評量** | 您最多可以在單一群組中新增 35,000 部機器。<br/><br/> 您最多可以在單一評估中評估35000部機器。

[深入了解](concepts-assessment-calculation.md)評估。

## <a name="physical-server-requirements"></a>實體伺服器需求

| **支援**                | **詳細資料**               
| :-------------------       | :------------------- |
| **實體伺服器部署**       | 實體伺服器可以是獨立的，也可以部署在叢集中。 |
| **權限**           | **Windows：** 對已加入網域的機器使用網域帳戶，並且未加入網域的機器使用本機帳戶。 使用者帳戶應新增至下列群組：遠端管理使用者、效能監視器使用者，以及效能記錄使用者。 <br/><br/> **Linux：** 您在要探索的 Linux 伺服器上必須要有根帳戶。 <br/> 或者，請務必使用下列命令來設定所需的功能。 <br/> setcap CAP_DAC_READ_SEARCH+eip /usr/sbin/fdisk <br/> setcap CAP_DAC_READ_SEARCH+eip /sbin/fdisk (如果 /usr/sbin/fdisk 不存在) <br/> setcap "cap_dac_override，cap_dac_read_search，cap_fowner，cap_fsetid，cap_setuid，cap_setpcap，cap_net_bind_service，cap_net_admin，cap_sys_chroot，cap_sys_admin，cap_sys_resource，cap_audit_control，cap_setfcap = + eip"/sbin/lvm <br/> setcap CAP_DAC_READ_SEARCH + eip/usr/sbin/dmidecode <br/> chmod a + r/sys/class/dmi/id/product_uuid
| **作業系統** | 所有 Windows 和 Linux 作業系統都可進行評估以進行遷移。 |


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate 設備需求

Azure Migrate 會使用 [Azure Migrate 設備](migrate-appliance.md)來進行探索和評估。 實體伺服器的設備可以在 VM 或實體機器上執行。 

- 瞭解實體伺服器的 [設備需求](migrate-appliance.md#appliance---physical) 。
- 了解設備必須在[公用](migrate-appliance.md#public-cloud-urls)和[政府](migrate-appliance.md#government-cloud-urls)雲端中存取的 URL。
- 您可以使用從 Azure 入口網站下載的 [PowerShell 腳本](how-to-set-up-appliance-physical.md) 來設定設備。
在 Azure Government 中， [使用此腳本](deploy-appliance-script-government.md)部署設備。

## <a name="port-access"></a>連接埠存取

下表摘要說明評量的埠需求。

**裝置** | **[連接]**
--- | ---
**設備** | TCP 埠3389上的輸入連線，以允許設備的遠端桌面連線。<br/><br/> 埠44368上的輸入連線，用來從遠端存取使用 URL 的設備管理應用程式： ``` https://<appliance-ip-or-name>:44368 ```<br/><br/> 埠443上的輸出連線 (HTTPS) ，以將探索和效能中繼資料傳送至 Azure Migrate。
**實體伺服器** | **Windows：** WinRM 埠5985上的輸入連線 (HTTP) ，從 Windows 伺服器提取設定和效能中繼資料。 <br/><br/> **Linux：**  埠22上的輸入連線 (TCP) ，以從 Linux 伺服器提取設定和效能中繼資料。 |

## <a name="agent-based-dependency-analysis-requirements"></a>代理程式型相依性分析需求

[相依性分析](concepts-dependency-visualization.md)可協助您識別所要評估並遷移至 Azure 的內部部署機器之間的相依性。 下表摘要說明用於設定代理程式型相依性分析的需求。 目前只有以代理程式為基礎的相依性分析支援實體伺服器。

**需求** | **詳細資料** 
--- | --- 
**部署之前** | 請先備妥 Azure Migrate 專案，並已在專案中新增伺服器評估工具。<br/><br/>  請在設定 Azure Migrate 設備以探索內部部署機器之後，部署相依性視覺效果。<br/><br/> [了解如何](create-manage-projects.md)第一次建立專案。<br/> [了解如何](how-to-assess.md)將評估工具新增至現有專案。<br/> 了解如何設定 Azure Migrate 設備，以評估 [Hyper-V](how-to-set-up-appliance-hyper-v.md)、[VMware](how-to-set-up-appliance-vmware.md) 或實體伺服器。
**Azure Government** | 在 Azure Government 中無法使用相依性視覺效果。
**Log Analytics** | Azure Migrate 會使用 [Azure 監視器記錄](../azure-monitor/log-query/log-query-overview.md)中的[服務對應](../azure-monitor/insights/service-map.md)解決方案來實現相依性視覺效果。<br/><br/> 請將新的或現有的 Log Analytics 工作區與 Azure Migrate 專案建立關聯。 Azure Migrate 專案的工作區在新增之後就無法進行修改。 <br/><br/> 此工作區必須位於和 Azure Migrate 專案相同的訂用帳戶中。<br/><br/> 此工作區必須位於「美國東部」、「東南亞」或「西歐」區域。 其他區域中的工作區則無法與專案相關聯。<br/><br/> 此工作區必須位於[支援服務對應](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions)的區域中。<br/><br/> 在 Log Analytics 中，與 Azure Migrate 相關聯的工作區會標記上遷移專案金鑰和專案名稱。
**所需的代理程式** | 在您要分析的每部機器上安裝下列代理程式：<br/><br/> [Microsoft Monitoring agent (MMA)](../azure-monitor/platform/agent-windows.md)。<br/> [相依性代理程式](../azure-monitor/platform/agents-overview.md#dependency-agent)。<br/><br/> 如果內部部署機器未連線到網際網路，則需要下載 Log Analytics 閘道並安裝到機器上。<br/><br/> 深入了解如何安裝 [Dependency Agent](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) 和 [MMA](how-to-create-group-machine-dependencies.md#install-the-mma)。
**Log Analytics 工作區** | 此工作區必須位於和 Azure Migrate 專案相同的訂用帳戶中。<br/><br/> Azure Migrate 可支援位於美國東部、東南亞和西歐區域的工作區。<br/><br/>  此工作區必須位於[支援服務對應](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions)的區域中。<br/><br/> Azure Migrate 專案的工作區在新增之後就無法進行修改。
**成本** | 服務對應解決方案在使用的前 180 天 (從您將 Log Analytics 工作區關聯至 Azure Migrate 專案的那一天算起) 不會產生任何費用。<br/><br/> 經過 180 天後，會套用標準 Log Analytics 費用。<br/><br/> 在相關聯的 Log Analytics 工作區中使用非服務對應的解決方案則會產生 Log Analytics 的[標準費用](https://azure.microsoft.com/pricing/details/log-analytics/)。<br/><br/> 刪除 Azure Migrate 專案時，工作區不會隨之刪除。 在專案刪除後，服務對應就無法免費使用，系統會根據 Log Analytics 工作區的付費層向每個節點收取費用。<br/><br/>如果您的專案是在 Azure Migrate 正式發行 (GA - 2018 年 2 月 28 日) 之前建立的，則可能會產生額外的服務對應費用。 為確保在 180 天後才要付費，建議您建立新的專案，因為 GA 之前的既有工作區仍要付費。
**管理** | 當您向工作區註冊代理程式時，請使用 Azure Migrate 專案所提供的識別碼和金鑰。<br/><br/> 您可以使用 Azure Migrate 外部的 Log Analytics 工作區。<br/><br/> 如果您刪除相關聯的 Azure Migrate 專案，工作區並不會自動刪除。 [請手動將其刪除](../azure-monitor/platform/manage-access.md)。<br/><br/> 除非您刪除 Azure Migrate 專案，否則請勿刪除 Azure Migrate 建立的工作區。 如果這樣做，相依性視覺效果功能不會如預期般運作。
**網際網路連線** | 如果機器未連線到網際網路，則需要在機器上安裝 Log Analytics 閘道。
**Azure Government** | 不支援代理程式型的相依性分析。

## <a name="next-steps"></a>後續步驟

[準備進行實體伺服器評](tutorial-prepare-physical.md)量。

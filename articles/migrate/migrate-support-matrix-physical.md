---
title: 支援 Azure 移植中的物理伺服器評估
description: 透過 Azure 移轉伺服器評估瞭解對物理伺服器評估的支援
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: ae76a6b570ec58e71a8a1728a2a601728030f58c
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538149"
---
# <a name="support-matrix-for-physical-server-assessment"></a>實體伺服器評估的支援矩陣 

本文使用[Azure 遷移:伺服器評估](migrate-services-overview.md#azure-migrate-server-assessment-tool)工具匯總了用於遷移到 Azure 的物理伺服器時的先決條件和支援要求。 如果要將物理伺服器移到 Azure,請查看[移轉支援矩陣](migrate-support-matrix-physical-migration.md)。


要評估物理伺服器,請創建 Azure 移轉專案,並將伺服器評估工具添加到專案中。 新增該工具後,將部署 Azure[移轉裝置](migrate-appliance.md)。 設備不斷發現本地電腦,並將計算機中數據和性能數據發送到 Azure。 發現完成後,您將發現的計算機收集到組中,併為組運行評估。


## <a name="limitations"></a>限制

**支援** | **詳細資料**
--- | ---
**評估限制** | 您可以在單個[Azure 移轉專案中](migrate-support-matrix.md#azure-migrate-projects)發現和評估多達 35,000 台物理伺服器。
**專案限制** | 可以在 Azure 訂閱中創建多個專案。 除了物理伺服器之外,專案還可以包括 VMware VM 和超 VM,每個虛擬機都達到評估限制。
**發現** | Azure 遷移設備最多可以發現 250 台物理伺服器。
**評量** | 單個組中最多可以添加 35,000 台電腦。<br/><br/> 您可以在單個評估中評估多達 35,000 台機器。

[瞭解有關評估的更多](concepts-assessment-calculation.md)詳細資訊。

## <a name="physical-server-requirements"></a>實體伺服器需求

| **支援**                | **詳細資料**               
| :-------------------       | :------------------- |
| **實體伺服器部署**       | 物理伺服器可以是獨立的,也可以部署在群集中。 |
| **權限**           | **視窗:** 您需要在要發現的所有 Windows 伺服器上使用本地或域使用者帳戶。 使用者帳戶應添加到這些組:遠端桌面使用者、性能監視器使用者和性能日誌使用者。 <br/><br/> **Linux：** 您在要探索的 Linux 伺服器上必須要有根帳戶。 |
| **作業系統** | Azure 支援的所有[Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)和[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)作業系統(Windows Server 2003 和 SUSE Linux 除外)。|


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate 設備需求

Azure 遷移使用[Azure 遷移設備](migrate-appliance.md)進行發現和評估。 物理伺服器的設備可以在 VM 或物理電腦上運行。 使用從 Azure 入口下載的 PowerShell 腳本設置設備。

- 瞭解物理伺服器[的裝置要求](migrate-appliance.md#appliance---physical)。
- 了解產品[在公共和政府雲](migrate-appliance.md#public-cloud-urls)中需要訪問的[government](migrate-appliance.md#government-cloud-urls)URL。

## <a name="port-access"></a>連接埠存取

下表總結了評估的埠要求。

**裝置** | **Connection**
--- | ---
**套用** | TCP 連接埠 3389 上的入站連接,以允許遠端桌面連接到設備。<br/><br/> 連接埠 44368 上的入站連線,用於使用網址遠端存取裝置管理應用:``` https://<appliance-ip-or-name>:44368 ```<br/><br/> 埠 443 (HTTPS) 上的出站連接,用於向 Azure 遷移發送發現和性能中繼資料。
**實體伺服器** | **視窗:** WinRM 上的入站連接埠 5985 (HTTP) 和 5986 (HTTPS),以便從 Windows 伺服器中提取配置和性能中數據。 <br/><br/> **Linux:** 埠 22 (UDP) 上的入站連接,以便從 Linux 伺服器中提取配置和性能中資料。 |

## <a name="agent-based-dependency-analysis-requirements"></a>基於代理的依賴項分析要求

[依賴項分析](concepts-dependency-visualization.md)可説明您確定要評估和遷移到 Azure 的本地電腦之間的依賴關係。 該表總結了設置基於代理的依賴項分析的要求。 目前僅支援物理伺服器基於代理的依賴項分析。

**需求** | **詳細資料** 
--- | --- 
**部署前** | 應設置 Azure 遷移專案,將伺服器評估工具添加到專案中。<br/><br/>  設定 Azure 移植裝置以發現本地電腦後部署相依項可視化<br/><br/> [瞭解如何](create-manage-projects.md)首次創建專案。<br/> [瞭解如何](how-to-assess.md)將評估工具添加到現有專案。<br/> 瞭解如何設置 Azure 遷移設備以評估[Hyper-V、VMware](how-to-set-up-appliance-hyper-v.md)[VMware](how-to-set-up-appliance-vmware.md)或物理伺服器。
**Azure Government** | 依賴項可視化在 Azure 政府中不可用。
**Log Analytics** | Azure 遷移使用[Azure 監視器日誌](../log-analytics/log-analytics-overview.md)中的[服務映射](../operations-management-suite/operations-management-suite-service-map.md)解決方案進行依賴項可視化。<br/><br/> 將新的或現有的日誌分析工作區與 Azure 遷移專案相關聯。 添加 Azure 遷移專案的工作區後無法對其進行修改。 <br/><br/> 工作區必須與 Azure 遷移專案處於同一訂閱中。<br/><br/> 工作區必須位於美國東部、東南亞或西歐區域。 其他區域中的工作區不能與項目關聯。<br/><br/> 工作區必須位於[支援服務映射](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)的區域。<br/><br/> 在日誌分析中,與 Azure 遷移關聯的工作區將使用遷移專案鍵和專案名稱進行標記。
**必要的代理程式** | 在要分析的每台電腦上,安裝以下代理:<br/><br/> [微軟監控代理 (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> [相依項代理](../azure-monitor/platform/agents-overview.md#dependency-agent)程式 。<br/><br/> 如果本地電腦未連接到互聯網,則需要在它們上下載並安裝日誌分析閘道。<br/><br/> 瞭解有關安裝[依賴項代理](how-to-create-group-machine-dependencies.md#install-the-dependency-agent)和[MMA](how-to-create-group-machine-dependencies.md#install-the-mma)的更多詳細資訊。
**Log Analytics 工作區** | 工作區必須與 Azure 遷移專案處於同一訂閱中。<br/><br/> Azure 遷移支援駐留在美國東部、東南亞和西歐區域的工作區。<br/><br/>  工作區必須位於[支援服務映射](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)的區域。<br/><br/> 添加 Azure 遷移專案的工作區後無法對其進行修改。
**成本** | 服務映射解決方案在前 180 天內不產生任何費用(從將日誌分析工作區與 Azure 遷移專案關聯的當天起)/<br/><br/> 經過 180 天後，會套用標準 Log Analytics 費用。<br/><br/> 在關聯的紀錄分析工作區中使用服務對應以外的任何解決方案將會產生紀錄分析[的標準費用](https://azure.microsoft.com/pricing/details/log-analytics/)。<br/><br/> 刪除 Azure Migrate 專案時，工作區不會隨之刪除。 刪除專案後,服務映射使用不是免費的,每個節點將按日誌分析工作區的付費層收費/<br/><br/>如果在 Azure 遷移通用性(GA- 2018 年 2 月 28 日)之前創建的專案,則可能已產生額外的服務映射費用。 為了確保僅在 180 天后付款,我們建議您創建新項目,因為 GA 之前的現有工作區仍可收費。
**管理** | 將代理註冊到工作區時,可以使用 Azure 遷移專案提供的 ID 和密鑰。<br/><br/> 您可以使用 Azure Migrate 外部的 Log Analytics 工作區。<br/><br/> 如果刪除關聯的 Azure 遷移專案,則不會自動刪除工作區。 [手動刪除它](../azure-monitor/platform/manage-access.md)。<br/><br/> 除非刪除 Azure 遷移項目,否則不要刪除 Azure 遷移創建的工作區。 如果這樣做，相依性視覺效果功能不會如預期般運作。
**網際網路連線** | 如果電腦未連接到互聯網,則需要在它們上安裝日誌分析閘道。
**Azure Government** | 不支援基於代理的依賴項分析。

## <a name="next-steps"></a>後續步驟

[準備物理伺服器評估](tutorial-prepare-physical.md)。

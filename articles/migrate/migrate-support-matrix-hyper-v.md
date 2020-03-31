---
title: 支援 Azure 遷移中的 Hyper-V 評估
description: 通過 Azure 遷移伺服器評估瞭解對 Hyper-V 評估的支援
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: e8a698b110f19dff593a93a41e9d6f20eb80cdb0
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2020
ms.locfileid: "80388996"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Hyper-V 評估的支援矩陣

本文使用[Azure 遷移：伺服器評估](migrate-services-overview.md#azure-migrate-server-assessment-tool)工具匯總了評估 Hyper-V VM 以遷移到 Azure 時的先決條件和支援要求。 如果要將 Hyper-VM 遷移到 Azure，請查看[遷移支援矩陣](migrate-support-matrix-hyper-v-migration.md)。

要設置 Hyper-V VM 評估，請創建 Azure 遷移專案，並將伺服器評估工具添加到專案中。 添加該工具後，將部署 Azure[遷移設備](migrate-appliance.md)。 設備不斷發現本地電腦，並將電腦中繼資料和效能資料發送到 Azure。 發現完成後，您將發現的電腦收集到組中，並為組運行評估。


## <a name="limitations"></a>限制

**支援** | **詳細資料**
--- | ---
**評估限制** | 您可以在單個[Azure 遷移專案中](migrate-support-matrix.md#azure-migrate-projects)發現和評估多達 35，000 個 Hyper-VM。
**專案限制** | 可以在 Azure 訂閱中創建多個專案。 除了 Hyper-V VM 之外，專案還可以包括 VMware VM 和物理伺服器，每個伺服器都達到評估限制。
**發現** | Azure 遷移設備可以發現多達 5000 個超 V VM。<br/><br/> 設備可連接到多達 300 台 Hyper-V 主機。
**評量** | 單個組中最多可以添加 35，000 台電腦。<br/><br/> 您可以在單個評估組中評估多達 35，000 個 VM。

[瞭解有關評估的更多](concepts-assessment-calculation.md)詳細資訊。



## <a name="hyper-v-host-requirements"></a>Hyper-V 主機要求

| **支援**                | **詳細資料**               
| :-------------------       | :------------------- |
| **Hyper-V 主機**       | Hyper-V 主機可以是獨立的，也可以部署在群集中。<br/><br/> Hyper-V 主機可以運行 Windows 伺服器 2019、Windows 伺服器 2016 或 Windows 伺服器 2012 R2。<br/> 您無法評估位於執行 Windows Server 2012 的 Hyper-V 主機上的 VM。
| **許可權**           | 您需要對 Hyper-V 主機授予管理員許可權。 <br/> 如果不想分配管理員許可權，請創建本地或域使用者帳戶，並將使用者帳戶添加到這些組 - 遠端系統管理使用者、Hyper-V 管理員和效能監視器使用者。 |
| **PowerShell 遠端**   | 必須在每個 Hyper-V 主機上啟用[PowerShell 遠端處理](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/enable-psremoting?view=powershell-7)。 |
| **Hyper-V 複本**       | 如果使用 Hyper-V 副本（或者有多個 VM 具有相同的 VM 識別碼），並且發現使用 Azure 遷移的原始 VM 和複製 VM，則 Azure 遷移生成的評估可能不准確。 |


## <a name="hyper-v-vm-requirements"></a>超 V VM 要求

| **支援**                  | **詳細資料**               
| :----------------------------- | :------------------- |
| **作業系統** | 所有[Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)和[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)作業系統。 |
| **整合服務**       | [Hyper-V 整合服務](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services)必須在您評估的 VM 上運行，以便捕獲作業系統資訊。 |


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate 設備需求

Azure 遷移使用[Azure 遷移設備](migrate-appliance.md)進行發現和評估。 您可以使用從門戶下載的壓縮超 VHd 或使用[PowerShell 腳本](deploy-appliance-script.md)部署設備。

- 瞭解 Hyper-V 的設備[要求](migrate-appliance.md#appliance---hyper-v)。
- 瞭解設備需要訪問的[URL。](migrate-appliance.md#url-access)

## <a name="port-access"></a>埠訪問

下表總結了評估的埠要求。

**裝置** | **連接**
--- | ---
**應用** | TCP 埠 3389 上的入站連接，以允許遠端桌面連線到設備。<br/><br/> 埠 44368 上的入站連接，用於使用 URL 遠端存取裝置管理應用：``` https://<appliance-ip-or-name>:44368 ```<br/><br/> 埠 443 （HTTPS） 上的出站連接，用於向 Azure 遷移發送發現和性能中繼資料。
**Hyper-V 主機/群集** | WinRM 上的入站連接埠 5985 （HTTP） 和 5986 （HTTPS），以便使用通用訊息模型 （CIM） 會話提取 Hyper-V VM 的中繼資料和效能資料。

## <a name="agent-based-dependency-analysis-requirements"></a>基於代理的依賴項分析要求

[依賴項分析](concepts-dependency-visualization.md)可説明您確定要評估和遷移到 Azure 的本地電腦之間的依賴關係。 該表總結了設置基於代理的依賴項分析的要求。 Hyper-V 目前僅支援基於代理的依賴項視覺化。 

**要求** | **詳細資料** 
--- | --- 
**部署前** | 應設置 Azure 遷移專案，將伺服器評估工具添加到專案中。<br/><br/>  設置 Azure 遷移設備以發現本地電腦後部署依賴項視覺化<br/><br/> [瞭解如何](create-manage-projects.md)首次創建專案。<br/> [瞭解如何](how-to-assess.md)將評估工具添加到現有專案。<br/> 瞭解如何設置 Azure 遷移設備以評估[超 V VM。](how-to-set-up-appliance-hyper-v.md)
**Azure Government** | 依賴項視覺化在 Azure 政府中不可用。
**日誌分析** | Azure 遷移使用[Azure 監視器日誌](../log-analytics/log-analytics-overview.md)中的[服務映射](../operations-management-suite/operations-management-suite-service-map.md)解決方案進行依賴項視覺化。<br/><br/> 將新的或現有的日誌分析工作區與 Azure 遷移專案相關聯。 添加 Azure 遷移專案的工作區後無法對其進行修改。 <br/><br/> 工作區必須與 Azure 遷移專案處於同一訂閱中。<br/><br/> 工作區必須位於美國東部、東南亞或西歐區域。 其他區域中的工作區不能與專案關聯。<br/><br/> 工作區必須位於[支援服務映射](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)的區域。<br/><br/> 在日誌分析中，與 Azure 遷移關聯的工作區將使用遷移專案鍵和專案名稱進行標記。
**必需的代理** | 在要分析的每台電腦上，安裝以下代理：<br/><br/> [微軟監控代理 （MMA）](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> [依賴項代理](../azure-monitor/platform/agents-overview.md#dependency-agent)。<br/><br/> 如果本地電腦未連接到互聯網，則需要在它們上下載並安裝日誌分析閘道。<br/><br/> 瞭解有關安裝[依賴項代理](how-to-create-group-machine-dependencies.md#install-the-dependency-agent)和[MMA](how-to-create-group-machine-dependencies.md#install-the-mma)的更多詳細資訊。
**日誌分析工作區** | 工作區必須與 Azure 遷移專案處於同一訂閱中。<br/><br/> Azure 遷移支援駐留在美國東部、東南亞和西歐區域的工作區。<br/><br/>  工作區必須位於[支援服務映射](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)的區域。<br/><br/> 添加 Azure 遷移專案的工作區後無法對其進行修改。
**成本** | 服務映射解決方案在前 180 天內不產生任何費用（從將日誌分析工作區與 Azure 遷移專案關聯的當天起）/<br/><br/> 經過 180 天後，會套用標準 Log Analytics 費用。<br/><br/> 在關聯的日誌分析工作區中使用服務映射以外的任何解決方案將會產生日誌分析[的標準費用](https://azure.microsoft.com/pricing/details/log-analytics/)。<br/><br/> 刪除 Azure Migrate 專案時，工作區不會隨之刪除。 刪除專案後，服務映射使用不是免費的，每個節點將按日誌分析工作區的付費層收費/<br/><br/>如果在 Azure 遷移通用性（GA- 2018 年 2 月 28 日）之前創建的專案，則可能已產生額外的服務映射費用。 為了確保僅在 180 天后付款，我們建議您創建新專案，因為 GA 之前的現有工作區仍可收費。
**管理** | 將代理註冊到工作區時，可以使用 Azure 遷移專案提供的 ID 和金鑰。<br/><br/> 您可以使用 Azure Migrate 外部的 Log Analytics 工作區。<br/><br/> 如果刪除關聯的 Azure 遷移專案，則不會自動刪除工作區。 [手動刪除它](../azure-monitor/platform/manage-access.md)。<br/><br/> 除非刪除 Azure 遷移專案，否則不要刪除 Azure 遷移創建的工作區。 如果這樣做，相依性視覺效果功能不會如預期般運作。
**互聯網連接** | 如果電腦未連接到互聯網，則需要在它們上安裝日誌分析閘道。

## <a name="next-steps"></a>後續步驟

[準備超 V VM 評估](tutorial-prepare-hyper-v.md)

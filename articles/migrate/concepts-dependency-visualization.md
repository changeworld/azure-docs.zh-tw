---
title: Azure Migrate 中的相依性視覺效果
description: 概述伺服器評定服務中的評估計算，Azure Migrate
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: f24656d02e19f422ff26e6b06d1631a9128dff43
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587102"
---
# <a name="dependency-visualization"></a>相依性視覺效果

本文說明 Azure Migrate：伺服器評估中的相依性視覺效果。

## <a name="what-is-dependency-visualization"></a>什麼是相依性視覺效果？

相依性視覺效果可協助您識別您想要評估並遷移至 Azure 的內部部署機器之間的相依性。 

- 在 Azure Migrate：伺服器評估中，您會將機器收集到群組中，然後評估該群組。 相依性視覺效果可協助您更精確地將機器分組，並具有高信賴度的評量。
- 相依性視覺效果可讓您識別必須一起遷移的機器。 您可以識別機器是否正在使用中，或者是否可以解除委任，而不是遷移。
- 視覺化相依性有助於確保不會留下任何內容，並避免在遷移期間發生意外的中斷。
- 如果您不確定機器是否屬於您想要遷移至 Azure 的應用程式部署的一部分，視覺效果特別有用。


> [!NOTE]
> Azure Government 無法使用相依性視覺效果。

## <a name="agent-basedagentless-visualization"></a>以代理程式為基礎/無代理程式視覺效果

有兩個選項可以部署相依性視覺效果：

- **代理程式型**相依性視覺效果要求代理程式必須安裝在您想要分析的每個內部部署機器上。
- **無代理**程式：使用此選項時，您不需要在要交叉檢查的電腦上安裝代理程式。 此選項目前為預覽狀態，且僅適用于 VMware Vm。


## <a name="agent-based-visualization"></a>以代理程式為基礎的視覺效果

**需求** | **詳細資料** | **深入了解**
--- | --- | ---
**部署之前** | 您應該已備妥 Azure Migrate 專案，並將 Azure Migrate： Server 評估工具加入至專案。<br/><br/>  在設定 Azure Migrate 設備以探索您的內部部署機器之後，您會部署相依性視覺效果。 | [瞭解如何](create-manage-projects.md)第一次建立專案。<br/><br/> [瞭解如何](how-to-assess.md)將評估工具加入至現有的專案。<br/><br/> 瞭解如何設定 Azure Migrate 應用裝置，以進行[hyper-v](how-to-set-up-appliance-hyper-v.md)、 [VMware](how-to-set-up-appliance-vmware.md)或實體伺服器的評量。
**必要的代理程式** | 在您要分析的每部電腦上，安裝下列代理程式：<br/><br/> [Microsoft Monitoring agent （MMA）](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)。<br/><br/> [Dependency agent](../azure-monitor/platform/agents-overview.md#dependency-agent)。<br/><br/> 如果內部部署機器未連線到網際網路，您需要在其上下載並安裝 Log Analytics 閘道。 | 深入瞭解如何安裝[Dependency agent](how-to-create-group-machine-dependencies.md#install-the-dependency-agent)和[MMA](how-to-create-group-machine-dependencies.md#install-the-mma)。
**Log Analytics** | Azure Migrate 使用[Azure 監視器記錄](../log-analytics/log-analytics-overview.md)中的[服務對應](../operations-management-suite/operations-management-suite-service-map.md)解決方案來進行相依性視覺效果。<br/><br/> 將新的或現有的 Log Analytics 工作區與 Azure Migrate 專案建立關聯。 加入 Azure Migrate 專案的工作區之後，就無法修改它。 <br/><br/> 工作區必須與 Azure Migrate 專案位於相同的訂用帳戶中。<br/><br/> 工作區必須位於 [美國東部]、[東南亞] 或 [西歐] 區域。 其他區域中的工作區無法與專案相關聯。<br/><br/> 工作區必須位於[支援服務對應](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)的區域中。<br/><br/> 在 Log Analytics 中，與 Azure Migrate 相關聯的工作區會以遷移專案金鑰和專案名稱標記。
**損失** | 服務對應解決方案不會產生前180天的任何費用（從您將 Log Analytics 工作區關聯至 Azure Migrate 專案的那一天）/<br/><br/> 經過 180 天後，會套用標準 Log Analytics 費用。<br/><br/> 在相關聯的 Log Analytics 工作區中使用服務對應以外的任何解決方案，將會產生 Log Analytics 的[標準費用](https://azure.microsoft.com/pricing/details/log-analytics/)。<br/><br/> 刪除 Azure Migrate 專案時，工作區不會隨之刪除。 刪除專案之後，服務對應的使用量不是免費的，而且每個節點都會依據 Log Analytics 工作區的付費層收費<br/><br/>如果您的專案是在 Azure Migrate 正式運作（GA-28 2018 年2月）之前建立的，則可能會產生額外的服務對應費用。 為確保只在180天后付款，我們建議您建立新的專案，因為 GA 之前的現有工作區仍可計費。
**管理** | 當您向工作區註冊代理程式時，您會使用 Azure Migrate 專案所提供的識別碼和金鑰。<br/><br/> 您可以使用 Azure Migrate 外部的 Log Analytics 工作區。<br/><br/> 如果您刪除相關聯的 Azure Migrate 專案，則不會自動刪除工作區。 請[手動將其刪除](../azure-monitor/platform/manage-access.md)。<br/><br/> 除非您刪除 Azure Migrate 專案，否則請勿刪除 Azure Migrate 建立的工作區。 如果這樣做，相依性視覺效果功能不會如預期般運作。

## <a name="agentless-visualization"></a>無代理程式視覺效果


**需求** | **詳細資料** | **深入了解**
--- | --- | ---
**部署之前** | 您應該已備妥 Azure Migrate 專案，並將 Azure Migrate： Server 評估工具加入至專案。<br/><br/>  您會在設定 Azure Migrate 設備以探索內部部署 VMWare 機器之後，部署相依性視覺效果。 | [瞭解如何](create-manage-projects.md)第一次建立專案。<br/><br/> [瞭解如何](how-to-assess.md)將評估工具加入至現有的專案。<br/><br/> 瞭解如何設定 Azure Migrate 應用裝置以進行[VMware](how-to-set-up-appliance-vmware.md) vm 的評量。
**必要的代理程式** | 您想要分析的電腦上不需要代理程式。
**受支援的作業系統** | 審查支援無代理程式視覺效果的[作業系統](migrate-support-matrix-vmware.md#agentless-dependency-visualization)。
**VM** | **Vmware 工具**： vmware 工具必須在您想要分析的 vm 上安裝並執行。<br/><br/> **帳戶**：在 Azure Migrate 設備上，您必須新增可用來存取 vm 以進行分析的使用者帳戶。<br/><br/> **Windows vm**：使用者帳戶必須是電腦上的本機或網域系統管理員。<br/><br/> **Linux vm**：帳戶上需要根許可權。 或者，使用者帳戶需要/bin/netstat 和/bin/ls 檔案上的這兩項功能： CAP_DAC_READ_SEARCH 和 CAP_SYS_PTRACE。 | [深入瞭解](migrate-appliance.md)Azure Migrate 設備。
**VMware** | **vCenter**：設備需要具備唯讀存取權的 vCenter Server 帳戶，以及為虛擬機器 > 來賓作業啟用的許可權。<br/><br/> **ESXi 主機**：在執行您要分析之 Vm 的 ESXi 主機上，Azure Migrate 設備必須能夠連線到 TCP 通訊埠443。
**收集的資料** |  無代理程式相依性視覺效果的運作方式是從已啟用它的機器中，捕獲 TCP 連線資料。 相依性探索開始之後，應用裝置會每五分鐘輪詢一次，從機器收集這項資料：<br/> -TCP 連接。<br/> -具有作用中連接之進程的名稱。<br/> -使用作用中連接執行進程的已安裝應用程式名稱。<br/> -每個輪詢間隔偵測到的連線數目。


## <a name="next-steps"></a>後續步驟
- [設定相依性視覺效果](how-to-create-group-machine-dependencies.md)
- 試用 VMware Vm 的無代理程式相依性[視覺效果](how-to-create-group-machine-dependencies-agentless.md)。
- 查看關於相依性視覺效果的[常見問題](common-questions-discovery-assessment.md#what-is-dependency-visualization)。



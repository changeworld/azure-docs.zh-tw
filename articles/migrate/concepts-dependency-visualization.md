---
title: Azure Migrate 中的相依性視覺效果
description: 概述伺服器評定服務中的評估計算，Azure Migrate
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 65a99e230262ae05d34dc8c04e87252c15133fda
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425675"
---
# <a name="dependency-visualization"></a>相依性視覺效果

本文說明 Azure Migrate：伺服器評估中的相依性視覺效果功能。

相依性視覺效果可協助您瞭解您想要評估和遷移的機器之間的相依性。 當您想要評估具有較高層級信賴度的電腦時，通常會使用相依性對應。

- 在 Azure Migrate：伺服器評估中，您會將機器一起收集成群組以進行評量。 群組通常是由您要一起遷移的機器組成，而相依性視覺效果可協助您交叉檢查機器相依性，讓您可以精確地將機器分組。
- 您可以使用視覺效果，探索需要一起遷移的相互相依系統。 您可以識別執行中的系統是否仍在使用中，或是否可以解除委任系統，而不是遷移。
- 視覺化相依性有助於確保不會留下任何內容，並避免在遷移期間發生意外的中斷。
- 如果您不完全瞭解屬於應用程式一部分的機器，這項功能就特別有用，因此應該一起遷移至 Azure。


> [!NOTE]
> 在 Azure Government 中無法使用相依性視覺效果功能。

## <a name="agent-based-and-agentless"></a>代理程式型和無代理程式

有兩個選項可以部署相依性視覺效果：

- **無代理**程式相依性視覺效果：此選項目前為預覽狀態，僅適用于 VMware vm。 您不需要在電腦上安裝任何代理程式。 
    - 其運作方式是從已啟用它的機器中，捕獲 TCP 連接資料。 [詳細資訊](how-to-create-group-machine-dependencies-agentless.md)。
相依性探索開始之後，應用裝置會以五分鐘的輪詢間隔從機器收集資料。
    - 收集的資料如下：
        - TCP 連線
        - 具有使用中連接的進程名稱
        - 執行上述進程的已安裝應用程式名稱
        - 否。 在每個輪詢間隔偵測到的連線數
- **代理程式型**相依性視覺化：若要使用代理程式型相依性視覺效果，您必須在要分析的每個內部部署機器上，下載並安裝下列代理程式。  
    - 必須在每個機器上安裝 [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)。 [深入瞭解](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-mma)如何安裝 MMA 代理程式。
    - 必須在每個機器上安裝[相依性代理程式](../azure-monitor/platform/agents-overview.md#dependency-agent)。 [深入瞭解](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-dependency-agent)如何安裝 dependency agent。
    - 此外，如果您有無法連線至網際網路的機器，則必須在該機器上下載並安裝 Log Analytics 閘道。

## <a name="agent-based-requirements"></a>以代理程式為基礎的需求

### <a name="what-do-i-need-to-deploy-dependency-visualization"></a>部署相依性視覺效果的需求為何？

部署相依性視覺效果之前，您應該先備妥 Azure Migrate 專案，並將 Azure Migrate： Server 評估工具加入至專案。 在設定 Azure Migrate 設備以探索您的內部部署機器之後，您會部署相依性視覺效果。

[深入瞭解](how-to-assess.md)如何新增工具，以及如何部署[hyper-v](how-to-set-up-appliance-hyper-v.md)、 [VMware](how-to-set-up-appliance-vmware.md)或實體伺服器的應用裝置。


### <a name="how-does-it-work"></a>運作方式

Azure Migrate 使用[Azure 監視器記錄](../log-analytics/log-analytics-overview.md)中的[服務對應](../operations-management-suite/operations-management-suite-service-map.md)解決方案來進行相依性視覺效果。

- 若要利用相依性視覺效果，您必須將 Log Analytics 工作區（新的或現有的）與 Azure Migrate 專案建立關聯。
- 工作區必須與您建立 Azure Migrate 專案所在的訂用帳戶相同。
- Azure Migrate 支援位於美國東部、東南亞和西歐區域的工作區。 其他區域中的工作區無法與專案相關聯。 另請注意，工作區必須位於[支援服務對應](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)的區域中。
- 加入 Azure Migrate 專案的工作區之後，就無法修改它。
- 在 Log Analytics 中，與 Azure Migrate 相關聯的工作區會以遷移專案金鑰和專案名稱標記。

    ![瀏覽 Log Analytics 工作區](./media/concepts-dependency-visualization/oms-workspace.png)



### <a name="do-i-need-to-pay-for-it"></a>我需要支付多少費用？

相依性視覺效果需要服務對應和相關聯的 Log Analytics 工作區。 

- 服務對應解決方案在前180天不會產生任何費用。 這是您與 Log Analytics 工作區相關聯 Azure Migrate 專案的日期。
- 經過 180 天後，會套用標準 Log Analytics 費用。
- 在相關聯的 Log Analytics 工作區中使用服務對應以外的任何解決方案，將會產生[標準 Log analytics](https://azure.microsoft.com/pricing/details/log-analytics/)費用。
- 刪除 Azure Migrate 專案時，工作區不會隨之刪除。 刪除專案之後，服務對應使用量不是免費的，而且每個節點都會依據 Log Analytics 工作區的付費層收費。

[在此](https://azure.microsoft.com/pricing/details/azure-migrate/)深入了解 Azure Migrate 定價。

> [!NOTE]
> 如果您在2018年2月28日 Azure Migrate 正式推出之前建立了專案，則可能會產生額外的服務對應費用。 為確保您只會在180天后支付費用，我們建議您建立新的專案，因為在正式運作之前仍可計費現有的工作區。



### <a name="how-do-i-manage-the-workspace"></a>如何管理工作區？

- 當您向工作區註冊代理程式時，您會使用 Azure Migrate 專案所提供的識別碼和金鑰。
- 您可以使用 Azure Migrate 外部的 Log Analytics 工作區。
- 如果您刪除相關聯的 Azure Migrate 專案，則不會自動刪除工作區。 您必須[手動將其刪除](../azure-monitor/platform/manage-access.md)。
- 除非您刪除 Azure Migrate 專案，否則請勿刪除 Azure Migrate 建立的工作區。 如果這樣做，相依性視覺效果功能不會如預期般運作。

## <a name="next-steps"></a>後續步驟
- [使用機器相依性的群組機器](how-to-create-group-machine-dependencies.md)
- [深入了解](common-questions-discovery-assessment.md#what-is-dependency-visualization)相依性視覺效果的常見問題集。



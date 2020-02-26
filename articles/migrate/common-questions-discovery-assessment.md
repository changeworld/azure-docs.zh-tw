---
title: 常見問題-Azure Migrate 中的探索、評估和相依性分析
description: 取得 Azure Migrate 中探索、評估和相依性分析的常見問題解答。
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 179be4f5b39806c717e94789c9fe52842d2a07d8
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589227"
---
# <a name="common-questions-about-discovery-assessment-and-dependency-analysis"></a>探索、評估和相依性分析的相關常見問題

本文會回答有關 Azure Migrate 中探索、評估和相依性分析的常見問題。 如果您有其他問題，請參閱下列文章：

- 有關 Azure Migrate 的[一般問題](resources-faq.md)。
- Azure Migrate 設備的相關[問題](common-questions-appliance.md)。
- 伺服器遷移的相關[問題](common-questions-server-migration.md)。
- 在[Azure Migrate 論壇](https://aka.ms/AzureMigrateForum)上張貼問題



## <a name="how-many-vms-can-i-discover-with-an-appliance"></a>我可以使用設備探索多少部 Vm？

您最多可以探索10000個 VMware Vm，最多5000部 Hyper-v Vm，最多可達250部具有單一應用裝置的伺服器。 如果您的內部部署環境中有更多電腦，請參閱調整[hyper-v](scale-hyper-v-assessment.md)、 [VMware](scale-vmware-assessment.md)和[實體](scale-physical-assessment.md)評量。



## <a name="vm-size-changed-can-i-run-an-assessment-again"></a>VM 大小已變更。 我可以再次執行評量嗎？

Azure Migrate 設備會持續收集內部部署環境的相關資訊。 但評量是內部部署 Vm 的時間點快照集。 如果您在想要評估的 VM 上變更設定，請使用 [重新計算] 選項，以最新的變更來更新評量。

### <a name="how-do-i-discover-vms-in-a-multitenant-environment"></a>如何? 在多租使用者環境中探索 Vm 嗎？

- 針對 VMware，如果您的環境在租使用者之間共用，而且您不想要探索另一個租使用者的訂用帳戶中某個租使用者的 Vm，請建立 vCenter Server 認證，而只能存取您想要探索的 Vm。 然後，當您在 Azure Migrate 設備中啟動探索時，請使用這些認證。
- 針對 Hyper-v，探索會使用 Hyper-v 主機認證。 如果 Vm 共用相同的 Hyper-v 主機，目前沒有任何方法可以分隔探索。  


### <a name="do-i-need-vcenter-server-for-vmware-vm-discovery"></a>需要 VMWare VM 探索的 vCenter Server 嗎？

是，Azure Migrate 需要 vCenter Server 在 VMware 環境中執行探索。 它不支援探索不受 vCenter Server 管理的 ESXi 主機。


## <a name="whats-the-difference-sizing-options"></a>差異調整大小選項有哪些？

使用內部部署調整大小，Azure Migrate 不會考慮要進行評估的 VM 效能資料。 它會根據內部部署設定來評估 VM 大小。 使用以效能為基礎的調整大小，調整大小取決於使用量資料。

- 例如，如果內部部署 VM 具有4個核心和 8 GB 的記憶體（50% CPU 使用率和50% 的記憶體使用率），將會發生下列情況：
    - 內部部署調整大小會建議具有4個核心和 8 GB 記憶體的 Azure VM SKU。
    - 以效能為基礎的大小調整將會建議具有2個核心和 4 GB 記憶體的 VM SKU，因為會考慮使用率百分比。

- 同樣地，磁片大小取決於兩個評估屬性：調整大小準則和儲存體類型。
    - 如果調整大小準則是以效能為基礎，且儲存體類型是自動的，Azure Migrate 會在識別目標磁片類型（標準或高階）時，將磁片的 IOPS 和輸送量值納入考慮。
    - 如果調整大小準則是以效能為基礎，且儲存體類型為 premium，Azure Migrate 會根據內部部署磁片的大小來建議 premium 磁片 SKU。 當調整大小為 [內部部署] 且儲存體類型為 [標準] 或 [premium] 時，會將相同的邏輯套用至磁片大小調整。

## <a name="does-performance-historyutilization-impact-sizing"></a>效能歷程/使用方式是否會影響大小？

這些屬性僅適用於以效能為基礎的調整大小。

- Azure Migrate 會收集內部部署機器的效能歷程記錄，並使用它來建議 Azure 中的 VM 大小和磁片類型。
- 設備會持續分析內部部署環境，以便每隔20秒收集一次即時使用方式資料。
- 設備會匯總20秒的樣本，並每隔15分鐘建立一個單一資料點。
- 若要建立資料點，設備會從所有20秒範例中選取尖峰值。
- 應用裝置會將此資料點傳送至 Azure。

當您在 Azure 中建立評估時，根據效能持續時間和效能歷程百分位數值，Azure Migrate 會計算有效的使用率值，並將其用於調整大小。

- 例如，如果您將 [效能持續時間] 設為 [一天]，並將 [百分位數] 值設定為 [95 個百分位數]，Azure Migrate 會以遞增順序排序收集器過去一天所傳送的15分鐘取樣點，並挑選第95個百分位數值作為有效的方面.
- 使用第95個百分位數值可確保忽略極端值。 如果您使用第99個百分位數，則可以包含極端值。 如果您想要挑選期間的尖峰使用量，但沒有遺漏任何極端值，請選取第99個百分位數。

## <a name="what-is-dependency-visualization"></a>什麼是相依性視覺效果？

使用相依性視覺效果來評估 Vm 群組，以便更安心地進行遷移。 相依性視覺效果會在您執行評量之前，先交叉檢查機器相依性。 它有助於確保不會留下任何內容，因此可在您遷移至 Azure 時避免非預期的中斷。 Azure Migrate 使用了 Azure 監視器中的 [服務對應] 解決方案來啟用相依性視覺效果。 [深入瞭解](concepts-dependency-visualization.md)。

> [!NOTE]
> Azure Government 無法使用相依性視覺效果。

## <a name="do-i-pay-for-dependency-visualization"></a>我要支付相依性視覺效果嗎？
否。 [深入了解](https://azure.microsoft.com/pricing/details/azure-migrate/) Azure Migrate 定價。

## <a name="what-do-i-install-for-dependency-visualization"></a>針對相依性視覺效果，我要安裝什麼？

若要使用相依性視覺效果，您需要在您想要評估的每個內部部署機器上，下載並安裝代理程式。

您必須在每部電腦上安裝下列代理程式：
- [Microsoft Monitoring Agent （MMA）](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)。
- [Dependency agent](../azure-monitor/platform/agents-overview.md#dependency-agent)。
- 如果您的電腦沒有網際網路連線，則需要下載並安裝 Log Analytics 閘道。

除非您要使用相依性視覺效果，否則不需要這些代理程式。

## <a name="can-i-use-an-existing-workspace"></a>我可以使用現有的工作區嗎？

是，您可以將現有的工作區附加至遷移專案，並將其用於相依性視覺效果。 

## <a name="can-i-export-the-dependency-visualization-report"></a>是否可以匯出相依性視覺效果報告？

否，無法匯出相依性視覺效果。 不過，Azure Migrate 會使用服務對應，而且您可以使用[服務對應 REST API](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections)來取得 JSON 格式的相依性。

## <a name="can-i-automate--mmadependency-agent-installation"></a>我可以將 MMA/Dependency agent 安裝自動化嗎？

使用此[腳本來安裝 Dependency agent](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples)。 遵循這些指示，使用命令列或自動化[來安裝 MMA](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) 。 針對 MMA，請使用[此腳本](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab)。

除了腳本以外，您也可以使用 Microsoft Endpoint Configuration Manager 和[Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration)等部署工具來部署代理程式。


## <a name="what-operating-systems-does-mma-support"></a>MMA 支援哪些作業系統？

- 查看[MMA 所支援的 Windows 作業系統](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)清單。
- 查看[MMA 所支援的 Linux 作業系統](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)清單。

## <a name="can-i-visualize-dependencies-for-more-than-an-hour"></a>我可以將相依性視覺化超過一小時嗎？
否。 您最多可以將相依性視覺化一小時。 您可以回到歷程記錄中的特定日期，一開始就是一個月，但視覺效果的最大持續時間為一小時。 例如，您可以使用相依性對應中的持續時間來查看昨天的相依性，但只能查看一個小時範圍內的相依性。 不過，您可以使用 Azure 監視器記錄，在較長的持續期間內查詢相依性[資料](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies)。

## <a name="can-visualize-dependencies-for-groups-of-more-than-10-vms"></a>可以將超過10個 Vm 的群組相依性視覺化嗎？
您可以將包含最多10個 Vm 的群組的相依性[視覺化](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies)。 如果您的群組具有10部以上的 Vm，建議您將群組分割成較小的群組，然後將相依性視覺化。




## <a name="next-steps"></a>後續步驟
閱讀[Azure Migrate 的總覽](migrate-services-overview.md)。

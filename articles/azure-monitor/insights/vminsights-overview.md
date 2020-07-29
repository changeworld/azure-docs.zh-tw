---
title: 適用於 VM 的 Azure 監視器是什麼？
description: 概述適用於 VM 的 Azure 監視器，其會監視 Azure Vm 的健全狀況和效能，並自動探索和對應應用程式元件及其相依性。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/22/2020
ms.openlocfilehash: f9ad39b88ad2212ea2cdceb40e61fbc0a2d1a764
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87320488"
---
# <a name="what-is-azure-monitor-for-vms"></a>適用於 VM 的 Azure 監視器是什麼？

適用於 VM 的 Azure 監視器會監視虛擬機器和虛擬機器擴展集的效能和健康情況，包括其執行中的進程和其他資源的相依性。 它可以藉由識別效能瓶頸和網路問題來協助提供重要應用程式的可預測效能和可用性，也可協助您瞭解問題是否與其他相依性有關。

適用於 VM 的 Azure 監視器支援下列各項的 Windows 和 Linux 作業系統：

- Azure 虛擬機器
- Azure 虛擬機器擴展集
- 與 Azure Arc 連線的混合式虛擬機器
- 內部部署虛擬機器
- 裝載于另一個雲端環境中的虛擬機器
  


>[!NOTE]
>我們最近[宣佈](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
)根據我們從公開預覽客戶收到的意見反應，對健全狀況功能進行的變更。 由於我們將進行的變更數目，我們將停止為新客戶提供健康情況功能。 現有的客戶可以繼續使用健全狀況功能。 如需詳細資訊，請參閱我們的[一般可用性常見問題](vminsights-ga-release-faq.md)。  


適用於 VM 的 Azure 監視器會將其資料儲存在 Azure 監視器記錄中，讓它能夠傳遞強大的匯總和篩選，以及分析一段時間的資料趨勢。 您可以直接從虛擬機器在單一 VM 中查看這項資料，或者您可以使用 Azure 監視器來傳遞多個 Vm 的匯總視圖。

![Azure 入口網站中的虛擬機器深入解析檢視方塊](media/vminsights-overview/vminsights-azmon-directvm.png)


## <a name="pricing"></a>定價
適用於 VM 的 Azure 監視器沒有直接成本，但您需支付其在 Log Analytics 工作區中的活動費用。 根據 [Azure 監視器價格頁面](https://azure.microsoft.com/pricing/details/monitor/)上所發佈的價格，適用於 VM 的 Azure 監視器會針對下列項目進行計費：

- 從代理程式內嵌資料，並將其儲存在工作區中。
- 根據記錄和健康情況資料的警示規則。
- 從警示規則傳送的通知。

記錄檔大小會因效能計數器的字串長度而異，而且可能會隨著配置給 VM 的邏輯磁片和網路介面卡數目增加。 如果您已經在使用服務對應，您會看到的唯一變更就是傳送至 Azure 監視器資料類型的其他效能資料 `InsightsMetrics` 。


## <a name="configuring-azure-monitor-for-vms"></a>設定適用於 VM 的 Azure 監視器
設定適用於 VM 的 Azure 監視器的步驟如下所示。 遵循每個連結以取得每個步驟的詳細指引：

- [建立 Log Analytics 工作區。](vminsights-configure-workspace.md#create-log-analytics-workspace)
- [將 VMInsights 解決方案新增至工作區。](vminsights-configure-workspace.md#add-vminsights-solution-to-workspace)
- [在要監視的虛擬機器和虛擬機器擴展集上安裝代理程式。](vminsights-enable-overview.md)



## <a name="next-steps"></a>後續步驟

- 如需為您的虛擬機器啟用監視的需求和方法，請參閱[部署適用於 VM 的 Azure 監視器](vminsights-enable-overview.md)。


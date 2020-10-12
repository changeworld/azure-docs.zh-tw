---
title: 適用於 VM 的 Azure 監視器是什麼？
description: 適用於 VM 的 Azure 監視器的總覽，可監視 Azure Vm 的健康情況和效能，並自動探索和對應應用程式元件及其相依性。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/22/2020
ms.openlocfilehash: 5c3cb13d0b2da9370f402083d82397679f2c9343
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89022491"
---
# <a name="what-is-azure-monitor-for-vms"></a>適用於 VM 的 Azure 監視器是什麼？

適用於 VM 的 Azure 監視器會監視虛擬機器和虛擬機器擴展集的效能和健康情況，包括其執行中的進程和其他資源的相依性。 它可找出效能瓶頸和網路問題，以協助提供重要應用程式的可預測效能和可用性，也可協助您瞭解問題是否與其他相依性有關。

適用於 VM 的 Azure 監視器支援下列各項的 Windows 和 Linux 作業系統：

- Azure 虛擬機器
- Azure 虛擬機器擴展集
- 與 Azure Arc 連線的混合式虛擬機器
- 內部部署虛擬機器
- 裝載于其他雲端環境中的虛擬機器
  



適用於 VM 的 Azure 監視器會將其資料儲存在 Azure 監視器記錄中，這可讓它提供強大的匯總和篩選，並分析一段時間的資料趨勢。 您可以直接從虛擬機器在單一 VM 中查看這項資料，也可以使用 Azure 監視器來提供多個 Vm 的匯總視圖。

![Azure 入口網站中的虛擬機器深入解析檢視方塊](media/vminsights-overview/vminsights-azmon-directvm.png)


## <a name="pricing"></a>定價
適用於 VM 的 Azure 監視器不需要直接付費，但會向您收取 Log Analytics 工作區中其活動的費用。 根據 [Azure 監視器價格頁面](https://azure.microsoft.com/pricing/details/monitor/)上所發佈的價格，適用於 VM 的 Azure 監視器會針對下列項目進行計費：

- 從代理程式內嵌並儲存在工作區中的資料。
- 以記錄和健康情況資料為基礎的警示規則。
- 從警示規則傳送的通知。

記錄大小會因效能計數器的字串長度而異，而且可以增加邏輯磁片數目和配置給 VM 的網路介面卡。 如果您已經在使用服務對應，則唯一會看到的變更是傳送至 Azure 監視器資料類型的其他效能資料 `InsightsMetrics` 。


## <a name="configuring-azure-monitor-for-vms"></a>設定適用於 VM 的 Azure 監視器
設定適用於 VM 的 Azure 監視器的步驟如下所示。 請依照每個連結來取得每個步驟的詳細指引：

- [建立 Log Analytics 工作區。](vminsights-configure-workspace.md#create-log-analytics-workspace)
- [將 VMInsights 方案新增至工作區。](vminsights-configure-workspace.md#add-vminsights-solution-to-workspace)
- [在要監視的虛擬機器和虛擬機器擴展集上安裝代理程式。](vminsights-enable-overview.md)



## <a name="next-steps"></a>後續步驟

- 如需啟用虛擬機器監視的需求和方法，請參閱 [部署適用於 VM 的 Azure 監視器](vminsights-enable-overview.md) 。


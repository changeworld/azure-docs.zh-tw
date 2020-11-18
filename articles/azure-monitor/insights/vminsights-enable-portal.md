---
title: 針對 Azure 入口網站中的單一虛擬機器或虛擬機器擴展集啟用 Azure 監視器
description: 瞭解如何使用 Azure 入口網站在單一 Azure 虛擬機器或虛擬機器擴展集上啟用適用於 VM 的 Azure 監視器。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 55e5ff2af62c903efeab5c4932eae0c9dc9b535c
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842306"
---
# <a name="enable-azure-monitor-for-single-virtual-machine-or-virtual-machine-scale-set-in-the-azure-portal"></a>針對 Azure 入口網站中的單一虛擬機器或虛擬機器擴展集啟用 Azure 監視器
本文說明如何使用 Azure 入口網站來啟用虛擬機器或虛擬機器擴展集的適用於 VM 的 Azure 監視器。 此程式可用於下列各項：

- Azure 虛擬機器
- Azure 虛擬機器擴展集
- 與 Azure Arc 連線的混合式虛擬機器

## <a name="prerequisites"></a>先決條件

- [建立並設定 Log Analytics 工作區](vminsights-configure-workspace.md)。 或者，您可以在此程式期間建立新的工作區。
- 請參閱 [支援的作業系統](vminsights-enable-overview.md#supported-operating-systems) ，以確保支援您所啟用之虛擬機器或虛擬機器擴展集的作業系統。 

## <a name="enable-azure-monitor-for-vms"></a>啟用適用於 VM 的 Azure 監視器

從 Azure 入口網站中，選取 [ **虛擬機器**]、[ **虛擬機器擴展集**] 或 [ **伺服器]-Azure Arc** ，然後從清單中選取資源。 在功能表的 [ **監視** ] 區段中，選取 [ **Insights** ]，然後 **啟用**。 下列範例顯示 Azure 虛擬機器，但功能表類似于 Azure 虛擬機器擴展集或 Azure Arc。

![為 VM 啟用適用於 VM 的 Azure 監視器](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

如果虛擬機器尚未連線到 Log Analytics 工作區，則會提示您選取其中一個。 如果您先前未 [建立工作區](../../azure-monitor/learn/quick-create-workspace.md)，則可以針對在訂用帳戶中部署虛擬機器或虛擬機器擴展集的位置，選取預設值。 將會建立並設定此工作區（如果尚未存在）。 如果您選取現有的工作區，它會設定為適用於 VM 的 Azure 監視器（如果尚未這麼做）。

> [!NOTE]
> 如果您選取先前未針對適用於 VM 的 Azure 監視器設定的工作區，則會將 *VMInsights* 管理元件新增至此工作區。 這會套用到任何已連線至工作區的代理程式，無論它是否已針對適用於 VM 的 Azure 監視器啟用。 系統會從這些虛擬機器收集效能資料，並儲存在 *InsightsMetrics* 資料表中。

![選取工作區](media/vminsights-configure-workspace/select-workspace.png)

當執行設定時，您會收到狀態訊息。

>[!NOTE]
>如果您針對虛擬機器擴展集使用手動升級模型，請升級實例以完成設定。 您可以從 [**設定**] 區段中的 [**實例**] 頁面開始升級。

![啟用適用於 VM 的 Azure 監視器來監視部署處理](media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)



## <a name="next-steps"></a>後續步驟

* 請參閱 [使用適用於 VM 的 Azure 監視器對應](vminsights-maps.md) 來查看探索到的應用程式相依性。 
* 請參閱 [查看 AZURE VM 效能](vminsights-performance.md) 以找出瓶頸、整體使用率和 VM 的效能。

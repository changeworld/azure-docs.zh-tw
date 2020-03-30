---
title: 在 Azure 門戶中為 VM 啟用 Azure 監視器
description: 瞭解如何在單個 Azure 虛擬機器或虛擬機器規模集中為 VM 計算 Azure 監視器。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 45bc8f16a547d4a95820f9dcd02132844b3be83c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480703"
---
# <a name="enable-azure-monitor-for-vms-in-the-azure-portal"></a>在 Azure 門戶中為 VM 啟用 Azure 監視器

本文介紹如何使用 Azure 門戶在少數 Azure 虛擬機器 （VM） 上啟用 VM 的 Azure 監視器。 您的目標是監視 VM 併發現任何性能或可用性問題。 

在開始之前，請查看[先決條件](vminsights-enable-overview.md)並確保訂閱和資源滿足要求。  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>啟用單個 Azure VM 的監視
要啟用監視 Azure VM：

1. 登錄到 Azure[門戶](https://portal.azure.com)。

1. 選取 [虛擬機器]****。

1. 從清單中選取 VM。

1. 在 VM 頁上，在 **"監視**"部分中，選擇 **"見解"，** 然後**啟用**。

    ![為 VM 啟用適用於 VM 的 Azure 監視器](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

1. 在 [將 Azure 監視器 Insights 上線]**** 頁面上，如果您目前在同一個訂用帳戶中有 Log Analytics 工作區，請在下拉式清單中選取它。  

    清單會預先選取用來在訂用帳戶中部署 VM 的預設工作區和位置。 

    >[!NOTE]
    >若要建立新的 Log Analytics 工作區以儲存來自 VM 的監視資料，請參閱[建立 Log Analytics 工作區](../../azure-monitor/learn/quick-create-workspace.md)。 您的 Log Analytics 工作區必須屬於其中一個[支援的區域](vminsights-enable-overview.md#log-analytics)。

6. 執行配置時，您將收到狀態訊息。

    ![啟用適用於 VM 的 Azure 監視器來監視部署處理](media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>啟用單個虛擬機器規模集的監視

要啟用監視 Azure 虛擬機器規模集：

1. 登錄到 Azure[門戶](https://portal.azure.com)。

2. 選擇**虛擬機器縮放集**。

3. 從清單中選擇虛擬機器縮放集。

4. 在虛擬機器縮放集頁上，在 **"監視**"部分中，選擇 **"見解****"，然後啟用**。

5. 在 **"見解"** 頁上，如果要使用現有的日誌分析工作區，請在下拉清單中選擇它。

    該清單預先選擇 VM 在訂閱中部署到的預設工作區和位置。 

    ![為虛擬機器規模集啟用 VM 的 Azure 監視器](media/vminsights-enable-single-vm/enable-vminsights-vmss-portal.png)

    >[!NOTE]
    >要創建新的日誌分析工作區以存儲虛擬機器規模集中的監視資料，請參閱[創建日誌分析工作區](../learn/quick-create-workspace.md)。 您的 Log Analytics 工作區必須屬於其中一個[支援的區域](vminsights-enable-overview.md#log-analytics)。

6. 執行配置時，您將收到狀態訊息。

    >[!NOTE]
    >如果對秤集使用手動升級模型，請升級實例以完成設置。 您可以在 **"設置"** 部分的"**實例**"頁開始升級。
    
    ![啟用適用於 VM 的 Azure 監視器來監視部署處理](media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status.png)

現在，您已為 VM 或虛擬機器規模集啟用了監視，監視資訊可在 Azure 監視器中用於 VM 進行分析。 

## <a name="next-steps"></a>後續步驟

* 要查看已發現的應用程式依賴項，請參閱[對 VM 映射使用 Azure 監視器](vminsights-maps.md)。 
* 要識別瓶頸、總體利用率和 VM 的性能，請參閱[查看 Azure VM 性能](vminsights-performance.md)。

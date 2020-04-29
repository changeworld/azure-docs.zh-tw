---
title: 啟用 Azure 入口網站中的適用於 VM 的 Azure 監視器
description: 瞭解如何在單一 Azure 虛擬機器或虛擬機器擴展集上評估適用於 VM 的 Azure 監視器。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 45bc8f16a547d4a95820f9dcd02132844b3be83c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79480703"
---
# <a name="enable-azure-monitor-for-vms-in-the-azure-portal"></a>啟用 Azure 入口網站中的適用於 VM 的 Azure 監視器

本文說明如何使用 Azure 入口網站，在少量的 Azure 虛擬機器（Vm）上啟用適用於 VM 的 Azure 監視器。 您的目標是監視您的 Vm，並找出任何效能或可用性問題。 

開始之前，請先檢查[必要條件](vminsights-enable-overview.md)，並確定您的訂用帳戶和資源符合需求。  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>啟用單一 Azure VM 的監視
若要啟用 Azure VM 的監視：

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 選取 [虛擬機器]****。

1. 從清單中選取 VM。

1. 在 [VM] 頁面的 [**監視**] 區段中，選取 [**深入**解析]，然後按一下 [**啟用**]。

    ![為 VM 啟用適用於 VM 的 Azure 監視器](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

1. 在 [將 Azure 監視器 Insights 上線]**** 頁面上，如果您目前在同一個訂用帳戶中有 Log Analytics 工作區，請在下拉式清單中選取它。  

    清單會預先選取用來在訂用帳戶中部署 VM 的預設工作區和位置。 

    >[!NOTE]
    >若要建立新的 Log Analytics 工作區以儲存來自 VM 的監視資料，請參閱[建立 Log Analytics 工作區](../../azure-monitor/learn/quick-create-workspace.md)。 您的 Log Analytics 工作區必須屬於其中一個[支援的區域](vminsights-enable-overview.md#log-analytics)。

6. 在執行設定時，您將會收到狀態訊息。

    ![啟用適用於 VM 的 Azure 監視器來監視部署處理](media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>啟用單一虛擬機器擴展集的監視

若要啟用 Azure 虛擬機器擴展集的監視：

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 選取 [**虛擬機器擴展集**]。

3. 從清單中選取虛擬機器擴展集。

4. 在 [虛擬機器擴展集] 頁面的 [**監視**] 區段中，選取 [**深入**解析]，然後按一下 [**啟用**]。

5. 在 [**深入**解析] 頁面上，如果您想要使用現有的 Log Analytics 工作區，請在下拉式清單中選取它。

    此清單會會預先選取訂用帳戶中部署 VM 的預設工作區和位置。 

    ![啟用虛擬機器擴展集的適用於 VM 的 Azure 監視器](media/vminsights-enable-single-vm/enable-vminsights-vmss-portal.png)

    >[!NOTE]
    >若要建立新的 Log Analytics 工作區以儲存來自虛擬機器擴展集的監視資料，請參閱[建立 Log analytics 工作區](../learn/quick-create-workspace.md)。 您的 Log Analytics 工作區必須屬於其中一個[支援的區域](vminsights-enable-overview.md#log-analytics)。

6. 在執行設定時，您將會收到狀態訊息。

    >[!NOTE]
    >如果您對擴展集使用手動升級模型，請升級實例以完成安裝。 您可以在 [**設定**] 區段中，從 [**實例**] 頁面開始升級。
    
    ![啟用適用於 VM 的 Azure 監視器來監視部署處理](media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status.png)

既然您已啟用 VM 或虛擬機器擴展集的監視功能，監視資訊就可以在適用於 VM 的 Azure 監視器中進行分析。 

## <a name="next-steps"></a>後續步驟

* 若要查看探索到的應用程式相依性，請參閱[使用適用於 VM 的 Azure 監視器對應](vminsights-maps.md)。 
* 若要找出瓶頸、整體使用率和 VM 的效能，請參閱[查看 AZURE VM 效能](vminsights-performance.md)。

---
title: 啟用 Azure 入口網站中的適用於 VM 的 Azure 監視器
description: 瞭解如何在單一 Azure 虛擬機器或虛擬機器擴展集上評估適用於 VM 的 Azure 監視器。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2020
ms.openlocfilehash: 4cdb9390b3146df74f2cbe8eba7b170a5d11fb2c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85507053"
---
# <a name="enable-azure-monitor-for-single-vm-or-vmss-in-the-azure-portal"></a>為 Azure 入口網站中的單一 VM 或 VMSS 啟用 Azure 監視器
本文說明如何使用 Azure 入口網站來啟用單一虛擬機器或虛擬機器擴展集的適用於 VM 的 Azure 監視器。 此程式可用於下列各項：

- Azure 虛擬機器
- Azure 虛擬機器擴展集
- Azure Arc 機器

開始之前，請先檢查[必要條件](vminsights-enable-overview.md)，並確定您的訂用帳戶和資源符合需求。  

## <a name="enable-azure-monitor-for-vms"></a>啟用適用於 VM 的 Azure 監視器

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 選取 [**虛擬機器**]、[**虛擬機器擴展集**] 或 [**機器-Azure Arc**]。

1. 從清單中選取資源。

1. 在功能表的 [**監視**] 區段中，選取 [**深入**解析]，然後按一下 [**啟用**]。 下列範例顯示 Azure 虛擬機器，但功能表類似于 Azure VMSS 或 Azure Arc。

    ![為 VM 啟用適用於 VM 的 Azure 監視器](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

1. 如果 VM 尚未連線到 Log Analytics 工作區，系統會提示您選取一個。 如果您先前尚未[建立工作區](../../azure-monitor/learn/quick-create-workspace.md)，則可以為訂用帳戶中部署 VM 或 VMSS 的位置選取預設值。 將會建立並設定此工作區（如果尚未存在的話）。

2. 在執行設定時，您將會收到狀態訊息。

    >[!NOTE]
    >如果您對擴展集使用手動升級模型，請升級實例以完成安裝。 您可以在 [**設定**] 區段中，從 [**實例**] 頁面開始升級。

    ![啟用適用於 VM 的 Azure 監視器來監視部署處理](media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)



## <a name="next-steps"></a>後續步驟

* 若要查看探索到的應用程式相依性，請參閱[使用適用於 VM 的 Azure 監視器對應](vminsights-maps.md)。 
* 若要找出瓶頸、整體使用率和 VM 的效能，請參閱[查看 AZURE VM 效能](vminsights-performance.md)。

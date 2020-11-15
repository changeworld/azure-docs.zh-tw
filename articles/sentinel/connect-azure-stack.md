---
title: 將 Azure Stack Hub 虛擬機器上架到 Azure Sentinel |Microsoft Docs
description: 本文說明如何在 Azure Stack Hub 的虛擬機器上布建 Azure 監視器、更新及設定管理虛擬機器擴充功能，並使用 Sentinel 開始監視這些虛擬機器。
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 9ff70e7c05ca8de49f560fba3d59f0609785b8c4
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2020
ms.locfileid: "94636770"
---
# <a name="connect-azure-stack-hub-virtual-machines-to-azure-sentinel"></a>將 Azure Stack Hub 的虛擬機器連線到 Azure Sentinel




有了 Azure Sentinel，您就可以監視在 Azure 上執行的 Vm，並在單一位置 Azure Stack Hub。 若要在您的 Azure Stack 機器上進行 Azure Sentinel，您必須先將虛擬機器擴充功能新增至現有的 Azure Stack Hub 虛擬機器。 

連接 Azure Stack Hub 機器之後，請從可根據您的資料呈現見解的儀表板資源庫中選擇。 您可以根據自己的需求輕鬆地自訂這些儀表板。



## <a name="add-the-virtual-machine-extension"></a>新增虛擬機器擴充功能 

將 **Azure 監視器、更新和設定管理** 虛擬機器擴充功能新增至 Azure Stack Hub 上執行的虛擬機器。 

1. 在新的瀏覽器索引標籤上，登入您的 [Azure Stack Hub 入口網站](https://docs.microsoft.com/azure-stack/user/azure-stack-use-portal#access-the-portal)。
2. 移至 [ **虛擬機器** ] 頁面，選取您要使用 Azure Sentinel 保護的虛擬機器。 如需如何在 Azure Stack Hub 上建立虛擬機器的詳細資訊，請參閱使用 [Azure Stack Hub 入口網站建立 Windows SERVER vm](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) ，或 [使用 Azure Stack Hub 入口網站建立 Linux 伺服器 vm](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal)。
3. 選取 [擴充功能]  。 此時會列出安裝在此虛擬機器上的虛擬機器擴充功能。
4. 按一下 [新增]  索引標籤。[新增資源]  功能表刀鋒視窗隨即開啟，並顯示可用的虛擬機器擴充功能清單。 
5. 選取 [ **Azure 監視器]、[更新] 和** [設定管理] 延伸模組，然後按一下 [ **建立** ]。 [ **安裝延伸** 模組設定] 視窗隨即開啟。

   ![Azure 監視器、更新及設定管理設定](./media/connect-azure-stack/azure-monitor-extension-fix.png)  

   >[!NOTE]
   > 如果您沒有看到 marketplace 中列出的 **Azure 監視器、更新和設定管理** 延伸模組，請與您的 Azure Stack Hub 操作員聯繫，使其可供使用。

6. 在 [Azure Sentinel] 功能表上選取 [ **工作區設定** ]，接著選取 [ **Advanced** ]，然後將 **工作區識別碼** 和 **工作區金鑰 (主鍵)** 。 
1. 在 [Azure Stack Hub **安裝延伸** 模組] 視窗中，將它們貼到指定的欄位，然後按一下 **[確定]** 。
1. 擴充功能安裝完成後，其狀態會顯示為「布建 **成功** 」。 虛擬機器最多可能需要一小時的時間才會出現在 Azure Sentinel 入口網站中。

如需安裝和設定 Windows 代理程式的詳細資訊，請參閱 [連接 windows 電腦](../azure-monitor/platform/agent-windows.md#install-agent-using-setup-wizard)。

若要對 Linux 的代理程式問題進行疑難排解，請參閱[對 Azure Log Analytics Linux 代理程式進行疑難排解](../azure-monitor/platform/agent-linux-troubleshoot.md)。

在 Azure 上的 Azure Sentinel 入口網站中，您可以在 [ **虛擬機器** ] 底下，瞭解所有 vm 和電腦及其狀態。 

## <a name="clean-up-resources"></a>清除資源
當不再需要時，您可以透過 Azure Stack Hub 入口網站，從虛擬機器移除擴充功能。

若要移除擴充功能：

1. 開啟 **Azure Stack Hub 入口網站** 。
2. 移至 [虛擬機器]  頁面，選取您要從中移除擴充功能的虛擬機器。
3. 選取 [擴充功能]  ，然後選取擴充功能 **Microsoft.EnterpriseCloud.Monitoring** 。
4. 按一下 [ **卸載** ]，然後確認您的選取專案。

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- 從[常見的錯誤格式設備](connect-common-event-format.md)將資料串流至 Azure Sentinel。

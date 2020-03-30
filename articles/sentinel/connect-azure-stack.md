---
title: 將 Azure 堆疊虛擬機器裝到 Azure 哨兵 |微軟文檔
description: 本文介紹如何在 Azure Stack 虛擬機器上預配 Azure 監視器、更新和建構管理虛擬機器擴展，並開始使用 Sentinel 監視它們。
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
ms.openlocfilehash: a8213bd57936f95870324950204dbd6c1473739a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588513"
---
# <a name="connect-azure-stack-virtual-machines-to-azure-sentinel"></a>將 Azure 堆疊虛擬機器連接到 Azure 哨兵




使用 Azure 哨兵，可以在一個位置監視在 Azure 和 Azure 堆疊上運行的 VM。 要將 Azure 堆疊電腦裝板到 Azure Sentinel，首先需要將虛擬機器擴展添加到現有 Azure 堆疊虛擬機器。 

連接 Azure 堆疊電腦後，請從基於資料顯示見解的儀表板庫中選擇。 這些儀表板可以輕鬆自訂，以滿足您的需要。



## <a name="add-the-virtual-machine-extension"></a>添加虛擬機器副檔名 

將**Azure 監視器、更新和建構管理**虛擬機器擴展添加到 Azure 堆疊上運行的虛擬機器。 

1. 在新的瀏覽器選項卡中，登錄到 Azure[堆疊門戶](https://docs.microsoft.com/azure-stack/user/azure-stack-use-portal#access-the-portal)。
2. 轉到 **"虛擬機器"** 頁，選擇要使用 Azure Sentinel 保護的虛擬機器。 有關如何在 Azure 堆疊上創建虛擬機器的資訊，請參閱[使用 Azure 堆疊門戶創建 Windows 伺服器 VM](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal)或使用 Azure[堆疊門戶創建 Linux 伺服器 VM。](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal)
3. 選擇**擴展**。 此時會列出安裝在此虛擬機器上的虛擬機器擴充功能。
4. 按一下"**添加"** 選項卡。"**新資源**"功能表邊欄選項卡將打開並顯示可用虛擬機器擴展的清單。 
5. 選擇**Azure 監視器、更新和建構管理**擴展，然後按一下"**創建**"。 將打開 **"安裝擴展配置**"視窗。

   ![Azure 監視器、更新和建構管理設置](./media/connect-azure-stack/azure-monitor-extension-fix.png)  

   >[!NOTE]
   > 如果市場中未列出**Azure 監視器、更新和建構管理**擴展，請與 Azure Stack 運算子聯繫以使其可用。

6. 在 Azure 哨兵功能表上，選擇**工作區設置**後跟**高級**，然後複製**工作區 ID**和**工作區鍵（主鍵）。** 
1. 在 Azure 堆疊**安裝擴展視窗中**，將它們粘貼到指示的欄位中，然後按一下"**確定**"。
1. 擴展安裝完成後，其狀態將顯示為 **"預配成功**"。 虛擬機器最多可能需要一個小時才能顯示在 Azure Sentinel 門戶中。

有關安裝和配置 Windows 代理的詳細資訊，請參閱[連接 Windows 電腦](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard)。

若要對 Linux 的代理程式問題進行疑難排解，請參閱[對 Azure Log Analytics Linux 代理程式進行疑難排解](../azure-monitor/platform/agent-linux-troubleshoot.md)。

在 Azure 上的 Azure Sentinel 門戶中，在**虛擬機器**下，您可以概覽所有 VM 和電腦及其狀態。 

## <a name="clean-up-resources"></a>清除資源
當不再需要時，您可以透過 Azure Stack 入口網站從虛擬機器移除擴充功能。

若要移除擴充功能：

1. 開啟 **Azure Stack 入口網站**。
2. 移至 [虛擬機器]**** 頁面，選取您要從中移除擴充功能的虛擬機器。
3. 選取 [擴充功能]****，然後選取擴充功能 **Microsoft.EnterpriseCloud.Monitoring**。
4. 按一下 **"卸載**"並確認您的選擇。

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- 從[常見的錯誤格式設備](connect-common-event-format.md)將資料串流至 Azure Sentinel。

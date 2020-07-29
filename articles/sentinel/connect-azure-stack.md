---
title: 將您的 Azure Stack 虛擬機器上架到 Azure Sentinel |Microsoft Docs
description: 本文說明如何在 Azure Stack 虛擬機器上布建 Azure 監視器、更新和設定管理虛擬機器擴充功能，並使用 Sentinel 開始監視它們。
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77588513"
---
# <a name="connect-azure-stack-virtual-machines-to-azure-sentinel"></a>將 Azure Stack 虛擬機器連接到 Azure Sentinel




使用 Azure Sentinel，您可以監視在 Azure 上執行的 Vm，並在一個位置 Azure Stack。 若要將您的 Azure Stack 機 Azure Sentinel，您必須先將虛擬機器擴充功能新增至現有的 Azure Stack 虛擬機器。 

連接 Azure Stack 機之後，請從會根據您的資料呈現見解的儀表板資源庫中選擇。 這些儀表板可以根據您的需求輕鬆地進行自訂。



## <a name="add-the-virtual-machine-extension"></a>新增虛擬機器擴充功能 

將 [ **Azure 監視器]、[更新] 和**[設定管理] 虛擬機器擴充功能新增至在您的 Azure Stack 上執行的虛擬機器。 

1. 在新的瀏覽器索引標籤中，登入您的[Azure Stack 入口網站](https://docs.microsoft.com/azure-stack/user/azure-stack-use-portal#access-the-portal)。
2. 移至 [**虛擬機器**] 頁面，選取您想要使用 Azure Sentinel 保護的虛擬機器。 如需如何在 Azure Stack 上建立虛擬機器的相關資訊，請參閱使用[Azure Stack 入口網站建立 Windows SERVER vm](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal)或[使用 Azure Stack 入口網站建立 Linux 伺服器 vm](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal)。
3. 選取 [**延伸**模組]。 此時會列出安裝在此虛擬機器上的虛擬機器擴充功能。
4. 按一下 [**新增**] 索引標籤。[**新增資源**功能表] 分頁隨即開啟，並顯示可用的虛擬機器擴充功能清單。 
5. 選取 [ **Azure 監視器]、[更新] 和**[設定管理] 延伸模組，然後按一下 [**建立**]。 [**安裝延伸**模組設定] 視窗隨即開啟。

   ![Azure 監視器、更新和設定管理設定](./media/connect-azure-stack/azure-monitor-extension-fix.png)  

   >[!NOTE]
   > 如果您沒有看到在 marketplace 中列出的 [ **Azure 監視器]、[更新與設定管理**] 延伸模組，請聯繫您的 Azure Stack 操作員，讓它可供使用。

6. 在 [Azure Sentinel] 功能表上，依序選取 [**工作區設定**] 和 [ **Advanced**]，然後複製**工作區識別碼**和**工作區金鑰（主要金鑰）**。 
1. 在 [Azure Stack**安裝延伸**模組] 視窗中，將其貼到指定的欄位中，然後按一下 **[確定]**。
1. 延伸模組安裝完成後，其狀態會顯示為 [布建**成功**]。 最多可能需要一小時的時間，虛擬機器才會出現在 Azure Sentinel 入口網站中。

如需安裝和設定適用于 Windows 之代理程式的詳細資訊，請參閱[連接 windows 電腦](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard)。

若要對 Linux 的代理程式問題進行疑難排解，請參閱[對 Azure Log Analytics Linux 代理程式進行疑難排解](../azure-monitor/platform/agent-linux-troubleshoot.md)。

在 Azure Azure Sentinel 入口網站的 [**虛擬機器**] 底下，您會大致瞭解所有 vm 和電腦及其狀態。 

## <a name="clean-up-resources"></a>清除資源
當不再需要時，您可以透過 Azure Stack 入口網站從虛擬機器移除擴充功能。

若要移除擴充功能：

1. 開啟 **Azure Stack 入口網站**。
2. 移至 [虛擬機器]**** 頁面，選取您要從中移除擴充功能的虛擬機器。
3. 選取 [擴充功能]****，然後選取擴充功能 **Microsoft.EnterpriseCloud.Monitoring**。
4. 按一下 [**卸載**]，並確認您的選擇。

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- 從[常見的錯誤格式設備](connect-common-event-format.md)將資料串流至 Azure Sentinel。

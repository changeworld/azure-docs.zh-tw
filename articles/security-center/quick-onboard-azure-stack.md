---
title: 將 Azure Stack 虛擬機器登入至 Azure 資訊安全中心
description: 此快速入門說明如何在 Azure Stack 虛擬機器上佈建 Azure 監視器、更新和組態管理虛擬機器擴充功能。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 8982348a-0624-40c7-8a1e-642a523c7f6b
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2019
ms.author: memildin
ms.openlocfilehash: 1f0a11616fe996f02d6464d23724156906176041
ms.sourcegitcommit: 99d016949595c818fdee920754618d22ffa1cd49
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/15/2020
ms.locfileid: "84771150"
---
# <a name="quickstart-onboard-your-azure-stack-virtual-machines-to-security-center"></a>快速入門：將 Azure Stack 虛擬機器登入至資訊安全中心
將您的 Azure 訂用帳戶登入後，您即可從 Azure Stack Marketplace 新增 **Azure 監視器、更新和組態管理**虛擬機器擴充功能，讓資訊安全中心保護您在 Azure Stack 上執行的虛擬機器。

此快速入門說明如何在執行於 Azure Stack 的虛擬機器 (同時支援 Linux 和 Windows) 上新增 **Azure 監視器、更新和組態管理**虛擬機器擴充功能。

## <a name="prerequisites"></a>Prerequisites
若要開始使用資訊安全中心，您必須有 Microsoft Azure 訂用帳戶。 如果您沒有訂用帳戶，可以註冊[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。

請確定您具有資訊安全中心標準層的 Azure 訂用帳戶，再開始進行此快速入門。 如需升級指示，請參閱[將 Azure 訂用帳戶上架到資訊安全中心標準定價層](security-center-get-started.md)。 您可以免費試用資訊安全中心標準層 30 天。 若要深入了解，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/security-center/)。

## <a name="select-your-workspace-in-azure-security-center"></a>在 Azure 資訊安全中心中選取您的工作區

1. 登入 [Azure 入口網站](https://azure.microsoft.com/features/azure-portal/)。
2. 在 [Microsoft Azure]  功能表中，選取 [資訊安全中心]  。 [資訊安全中心 - 概觀]  隨即開啟。 

   ![資訊安全中心概觀][2]

3. 在 [資訊安全中心] 主功能表下，選取 [開始使用]  。
4. 選取 [開始使用]  索引標籤。

   ![開始使用][3]

5. 按一下 [新增非 Azure 電腦]  下方的 [設定]  。 隨即會顯示 Log Analytics 工作區清單。 清單中顯示啟用自動佈建之後，資訊安全中心為您建立的預設工作區 (如果適用)。 選取此工作區，或選取您要讓 Azure Stack VM 對其報告安全資料的其他工作區。

    ![新增非 Azure 電腦](./media/quick-onboard-windows-computer/non-azure.png)

   [直接代理程式]  刀鋒視窗提供下載代理程式的連結，以及用於設定代理程式的工作區識別碼金鑰。

   >[!NOTE]
   > 您不需要手動下載代理程式。 代理程式會在下列步驟中安裝為 VM 擴充功能。

6. 在 [工作區識別碼]  的右邊，選取複製圖示並將識別碼貼到記事本中。

7. 在 [主要金鑰]  的右邊，選取複製圖示並將識別碼貼到記事本中。

## <a name="add-the-virtual-machine-extension-to-your-existing-azure-stack-virtual-machines"></a>將虛擬機器擴充功能新增至您現有的 Azure Stack 虛擬機器
現在，您必須將 **Azure 監視器、更新和組態管理**虛擬機器擴充功能新增至執行於 Azure Stack 上的虛擬機器。

1. 在新的瀏覽器索引標籤中，登入您 **Azure Stack** 入口網站。
2. 移至 [虛擬機器]  頁面，選取要用資訊安全中心來保護的虛擬機器。 如需如何在 Azure Stack 上建立虛擬機器的詳細資訊，請參閱 [Windows 虛擬機器的此快速入門](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal)或 [Linux 虛擬機器的此快速入門](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal)。
3. 選取 [擴充功能]  。 此時會列出安裝在此虛擬機器上的虛擬機器擴充功能。
4. 按一下 [新增]  索引標籤。[新增資源]  功能表刀鋒視窗隨即開啟，並顯示可用的虛擬機器擴充功能清單。 
5. 選取 [Azure 監視器、更新和組態管理]  擴充功能，然後按一下 [建立]  。 [安裝擴充功能]  組態刀鋒視窗隨即開啟。

>[!NOTE]
> 如果您沒有看到 **Azure Monitor, Update and Configuration Management** 擴充在市集中列出，請洽詢您的 Azure Stack 操作員以使該擴充可供取得。

6. 在 [安裝擴充功能]  組態刀鋒視窗上，貼上您在先前程序中複製到「記事本」中的 [工作區識別碼]  和 [工作區金鑰 (主要金鑰)]  。
7. 提供必要的組態設定後，請按一下 [確定]  。
8. 擴充功能安裝完成後，其狀態會顯示為 [佈建成功]  。 虛擬機器最多可能需要一小時才會出現在資訊安全中心入口網站中。

如需為 Windows 安裝及設定代理程式的詳細資訊，請參閱[連線到 Windows 電腦](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard)。

若要對 Linux 的代理程式問題進行疑難排解，請參閱[對 Azure Log Analytics Linux 代理程式進行疑難排解](../azure-monitor/platform/agent-linux-troubleshoot.md)。

您現在可於同一處監視您的 Azure VM 和非 Azure 電腦。 在 Azure 資訊安全中心入口網站的 [計算]  下方，您可以檢視所有 VM 和電腦的概觀及建議。 資訊安全中心也會在安全性警示中顯示針對這些電腦所偵測到的任何項目。

  ![[計算] 刀鋒視窗][6]

[計算]  刀鋒視窗上顯示兩個類型的圖示：

![icon1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) 非 Azure 電腦 

![icon2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) Azure VM (Azure Stack VM 將會顯示在此群組中)

## <a name="clean-up-resources"></a>清除資源
當不再需要時，您可以透過 Azure Stack 入口網站從虛擬機器移除擴充功能。

若要移除擴充功能：

1. 開啟 **Azure Stack 入口網站**。
2. 移至 [虛擬機器]  頁面，選取您要從中移除擴充功能的虛擬機器。
3. 選取 [擴充功能]  ，然後選取擴充功能 **Microsoft.EnterpriseCloud.Monitoring**。
4. 按一下 [解除安裝]  ，並按一下 [是]  以確認您的選取。

## <a name="next-steps"></a>後續步驟
在此快速入門中，您已在執行於 Azure Stack 的虛擬機器上佈建 Azure 監視器、更新和組態管理擴充功能。 若要深入了解如何使用資訊安全中心，請繼續進行設定安全性原則及評估資源安全性的教學課程。

> [!div class="nextstepaction"]
> [教學課程：定義和評估安全性原則](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/get-started.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png

---
title: 將您的非 Azure 機器連線至 Azure 資訊安全中心
description: 瞭解如何將您的非 Azure 機器連線至安全中心
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 82b8161f92f337002e9d8bbdc45cd53d5921fc00
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91280669"
---
#  <a name="connect-your-non-azure-machines-to-security-center"></a>將您的非 Azure 機器連線至安全中心

資訊安全中心可以監視非 Azure 電腦的安全性狀態，但您需要先上架這些資源。 您可以 **從 [開始** 使用] 頁面或從 **清查** 新增非 Azure 電腦，如下所述。

## <a name="add-non-azure-computers"></a>新增非 Azure 電腦 

1. 在 [安全性中心] 功能表中，開啟 **[開始使用** ] 頁面。
1. 選取 [開始使用]  索引標籤。

    :::image type="content" source="./media/security-center-onboarding/onboarding-get-started-tab.png" alt-text="[開始使用] 頁面中的 [開始] 索引標籤" lightbox="./media/security-center-onboarding/onboarding-get-started-tab.png":::

1. 在下方 **新增非 Azure 伺服器**上，選取 [ **設定** ]。

    > [!TIP]
    > 您也可以從 **清查** 頁面的 [ **新增非 Azure 伺服器** ] 按鈕開啟 [新增電腦]。

    隨即會顯示 Log Analytics 工作區清單。 清單中顯示啟用自動佈建之後，資訊安全中心為您建立的預設工作區 (如果適用)。 選取此工作區或其他您要使用的工作區。

    您可以將電腦新增至現有的工作區，或建立新的工作區。 

1. （選擇性）若要建立新的工作區，請選取 [  **建立新的工作區**]。

1. 從工作區清單中，選取相關工作區的 [ **新增伺服器** ]。
    [ **代理程式管理** ] 頁面隨即出現。

    從這裡，根據您要上架的機器類型，選擇下列相關程式：

    - [上架 Azure Stack Vm](#onboard-your-azure-stack-vms)
    - [將您的 Linux 電腦上架](#onboard-your-linux-machines)
    - [將您的 Windows 電腦上架](#onboard-your-windows-machines)


### <a name="onboard-your-azure-stack-vms"></a>上架 Azure Stack Vm
若要新增 Azure Stack Vm，您需要 **代理程式管理** 頁面上的資訊，並在 Azure Stack 上執行的虛擬機器上設定 **Azure 監視器、更新和設定管理** 虛擬機器擴充功能。
1. 在 [ **代理程式管理** ] 頁面中，將 **工作區識別碼** 和 **主要金鑰** 複製到 [記事本]。
1. 登入您的 **Azure Stack** 入口網站，然後開啟 [ **虛擬機器** ] 頁面。
1. 選取您要使用「安全性中心」保護的虛擬機器。
    >[!TIP]
    > 如需如何在 Azure Stack 上建立虛擬機器的詳細資訊，請參閱 [Windows 虛擬機器的此快速入門](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal)或 [Linux 虛擬機器的此快速入門](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal)。
1. 選取 [擴充功能]  。 此時會列出安裝在此虛擬機器上的虛擬機器擴充功能。
1. 選取 [ **新增** ] 索引標籤。[ **新增資源** ] 功能表會顯示可用的虛擬機器擴充功能清單。
1. 選取 **Azure 監視器、更新和設定管理** 延伸模組，然後選取 [ **建立**]。 [ **安裝延伸** 模組設定] 頁面隨即開啟。
    >[!NOTE]
    > 如果您沒有看到 **Azure Monitor, Update and Configuration Management** 擴充在市集中列出，請洽詢您的 Azure Stack 操作員以使該擴充可供取得。
1. 在 [ **安裝延伸** 模組設定] 頁面上，貼上您在上一個步驟中複製到 [記事本] 的 [ **工作區識別碼** ] 和 [ **工作區金鑰] (主要金鑰) ** 。
1. 當您完成設定時，請選取 **[確定]**。 延伸模組的狀態將會顯示為「布建 **成功**」。 虛擬機器最多可能需要一小時的時間才會出現在 [安全中心] 中。


### <a name="onboard-your-linux-machines"></a>將您的 Linux 電腦上架
若要新增 Linux 電腦，您需要 **代理程式管理** 頁面中的 WGET 命令。
1. 在 [ **代理程式管理** ] 頁面中，將 **WGET** 命令複製到 [記事本]。 將此檔案儲存到可從您的 Linux 電腦存取的位置。
1. 在您的 Linux 電腦上，使用 WGET 命令開啟檔案。 選取整個內容，並將其複製並貼到終端主控台。
1. 當安裝完成時，您可以藉由執行*pgrep*命令來驗證是否已安裝*omsagent* 。 此命令會傳回 *omsagent* PID。
    您可以在下列位置找到代理程式的記錄： */var/opt/microsoft/omsagent/ \<workspace id> /log/* 可能需要30分鐘的時間，新的 Linux 機器才會出現在 [安全中心] 中。


### <a name="onboard-your-windows-machines"></a>將您的 Windows 電腦上架
若要新增 Windows 電腦，您需要 [代理程式 **管理** ] 頁面上的資訊，並下載適當的代理程式檔 (32/64 位) 。
1. 選取適用於您電腦處理器類型的 [下載 Windows 代理程式]  連結以下載安裝檔。
1. 在 [ **代理程式管理** ] 頁面中，將 **工作區識別碼** 和 **主要金鑰** 複製到 [記事本]。
1. 將下載的安裝檔複製到目的電腦，然後執行它。
1. 遵循安裝精靈 (**下**一**步，****我同意**，下**一**步是) 。
    1. 在 [ **Azure Log Analytics** ] 頁面上，貼上您複製到「記事本」 ** (主要金鑰) **的**工作區識別碼**和工作區金鑰。
    1. 如果電腦應該向 Azure Government cloud 中的 Log Analytics 工作區回報，請從 [ **Azure 雲端**] 下拉式清單中選取 [ **azure 美國政府**]。
    1. 如果電腦需要透過 Proxy 伺服器與 Log Analytics 服務進行通訊，請選取 [進階]  ，然後提供 Proxy 伺服器的 URL 和連接埠號碼。
    1. 當您輸入所有設定之後，請選取 **[下一步]**。
    1. 在 [ **準備安裝** ] 頁面上，檢查要套用的設定，然後選取 [ **安裝**]。
    1. 在 [設定成功完成]  頁面上，選取 [完成]  。

完成時，[Log Analytics 代理程式]  會出現在 [控制台]  中。 您可以在該處檢閱您的設定，並確認代理程式已連線。

如需安裝和設定代理程式的詳細資訊，請參閱 [連接 Windows 電腦](../azure-monitor/platform/agent-windows.md#install-agent-using-setup-wizard)。


## <a name="verifying"></a>驗證中
恭喜！ 現在您可以在單一位置看到您的 Azure 和非 Azure 機器。 開啟 [ [資產清查] 頁面](asset-inventory.md) ，並篩選至相關的資源類型。 這兩個圖示會區分類型：

  ![icon1](./media/quick-onboard-linux-computer/security-center-monitoring-icon1.png) 非 Azure 機器

  ![icon2](./media/quick-onboard-linux-computer/security-center-monitoring-icon2.png) Azure VM


## <a name="next-steps"></a>後續步驟

此頁面說明如何將您的非 Azure 機器新增至 Azure 資訊安全中心。 若要監視其狀態，請使用下列頁面所述的清查工具：

- [使用資產清查和管理工具探索及管理您的資源](asset-inventory.md)
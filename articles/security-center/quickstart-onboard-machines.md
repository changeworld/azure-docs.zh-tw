---
title: 將您的非 Azure 機器連線至 Azure 資訊安全中心
description: 了解如何將您的非 Azure 機器連線至資訊安全中心
author: memildin
ms.author: memildin
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
zone_pivot_groups: non-azure-machines
ms.openlocfilehash: 8d0d4726763e36bd889c2f564d116d454b2f62b9
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/23/2020
ms.locfileid: "97740601"
---
# <a name="connect-your-non-azure-machines-to-security-center"></a>將您的非 Azure 機器連線至資訊安全中心

資訊安全中心可以監視非 Azure 電腦的安全性狀態，但您需要先將其連線到 Azure。

您可以透過下列任何方式連線非 Azure 電腦：

- 使用已啟用 Azure Arc 的伺服器 (**建議**)
- 從資訊安全中心在 Azure 入口網站中的頁面 ([開始使用] 和 [清查])

本頁面會說明上述各方式。

::: zone pivot="azure-arc"

## <a name="add-non-azure-machines-with-azure-arc"></a>使用 Azure Arc 新增非 Azure 機器

將非 Azure 機器新增至 Azure 資訊安全中心時，已啟用 Azure Arc 的伺服器是慣用的方式。

已啟用 Azure Arc 的伺服器會成為 Azure 資源，並連同相關建議 (例如其他 Azure 資源) 出現在資訊安全中心中。

此外，已啟用 Azure Arc 的伺服器也提供增強功能，例如在機器上啟用來賓組態原則的選項、將 Log Analytics 代理程式部署為延伸模組、簡化其他 Azure 服務的部署等等。 如需其優點的概觀，請參閱[支援的案例](../azure-arc/servers/overview.md#supported-scenarios)。

深入了解[已啟用 Azure Arc 的伺服器](../azure-arc/servers/overview.md)。

**若要部署 Azure Arc：**

- 針對單一機器，請依照[快速入門：將混合式機器與已啟用 Azure Arc 的伺服器連線](../azure-arc/servers/learn/quick-enable-hybrid-vm.md)中的指示操作。
- 若要將多部機器大規模地連線到已啟用 Arc 的伺服器，請參閱[大規模地將混合式機器連線到 Azure](../azure-arc/servers/onboard-service-principal.md)

> [!TIP]
> 如果您要將在 AWS 上執行的機器上線，資訊安全中心中適用於 AWS 的連接器會以透明方式為您處理 Azure Arc 部署。 若要深入了解，請參閱[將您的 AWS 帳戶連線到 Azure 資訊安全中心](quickstart-onboard-aws.md)。

::: zone-end

::: zone pivot="azure-portal"

## <a name="add-non-azure-machines-from-the-azure-portal"></a>從 Azure 入口網站新增非 Azure 機器

1. 從資訊安全中心的功能表中，開啟 [開始使用] 頁面。
1. 選取 [開始使用]  索引標籤。

    :::image type="content" source="./media/security-center-onboarding/onboarding-get-started-tab.png" alt-text="開始使用頁面中的開始使用索引標籤" lightbox="./media/security-center-onboarding/onboarding-get-started-tab.png":::

1. 在 [新增非 Azure 伺服器] 底下，選取 [設定]。

    > [!TIP]
    > 您也可以從 [清查] 頁面的 [新增非 Azure 伺服器] 按鈕開啟 [新增機器]。
    > 
    > :::image type="content" source="./media/security-center-onboarding/onboard-inventory.png" alt-text="從資產清查頁面新增非 Azure 機器":::

    隨即會顯示 Log Analytics 工作區清單。 清單中顯示啟用自動佈建之後，資訊安全中心為您建立的預設工作區 (如果適用)。 選取此工作區或其他您要使用的工作區。

    您可以將電腦新增至現有的工作區，或建立新的工作區。

1. (選擇性) 若要建立新的工作區，請選取 [建立新的工作區]。

1. 從工作區清單中，針對相關的工作區選取 [新增伺服器]。
    [代理程式管理] 頁面隨即出現。

    在此頁面中，您可以根據要上線的機器類型選擇下列相關程序：

    - [將您的 Azure Stack VM 上線](#onboard-your-azure-stack-vms)
    - [將您的 Linux 機器上線](#onboard-your-linux-machines)
    - [將您的 Windows 機器上線](#onboard-your-windows-machines)

### <a name="onboard-your-azure-stack-vms"></a>將您的 Azure Stack VM 上線

若要新增 Azure Stack VM，您必須要有 [代理程式管理] 上的資訊，且必須將 **Azure 監視器、更新和組態管理** 虛擬機器擴充功能新增至執行於 Azure Stack 上的虛擬機器。

1. 從 [代理程式管理] 頁面，將 [工作區識別碼] 和 [主要金鑰] 複製到記事本中。
1. 登入您的 **Azure Stack** 入口網站，並開啟 [虛擬機器] 頁面。
1. 選取要用資訊安全中心來保護的虛擬機器。
    >[!TIP]
    > 如需如何在 Azure Stack 上建立虛擬機器的詳細資訊，請參閱 [Windows 虛擬機器的此快速入門](/azure-stack/user/azure-stack-quick-windows-portal)或 [Linux 虛擬機器的此快速入門](/azure-stack/user/azure-stack-quick-linux-portal)。
1. 選取 [擴充功能]  。 此時會列出安裝在此虛擬機器上的虛擬機器擴充功能。
1. 選取 [新增] 索引標籤。[新增資源] 功能表會顯示可用的虛擬機器擴充功能清單。
1. 選取 [Azure 監視器、更新和組態管理] 擴充功能，然後選取 [建立]。 [安裝擴充功能] 設定頁面隨即開啟。
    >[!NOTE]
    > 如果您沒有看到 **Azure Monitor, Update and Configuration Management** 擴充在市集中列出，請洽詢您的 Azure Stack 操作員以使該擴充可供取得。
1. 在 [安裝擴充功能] 設定頁面上，貼上您在先前的步驟中複製到「記事本」中的 [工作區識別碼] 和 [工作區金鑰 (主要金鑰)]。
1. 在完成設定後，選取 [確定]。 擴充功能的狀態會顯示為 [佈建成功]。 虛擬機器最多可能需要一小時才會出現在資訊安全中心中。

### <a name="onboard-your-linux-machines"></a>將您的 Linux 機器上線

若要新增 Linux 機器，您需要 [代理程式管理] 頁面中的 WGET 命令。

1. 從 [代理程式管理 ] 頁面將 **WGET** 命令複製到「記事本」中。 將此檔案儲存到可從您的 Linux 電腦存取的位置。
1. 在您的 Linux 電腦上，使用 WGET 命令開啟檔案。 選取整個內容，將其複製並貼到終端機主控台中。
1. 安裝完成後，您可以執行 *pgrep* 以驗證是否已安裝 *omsagent*。 此命令將會傳回 *omsagent* PID。
    代理程式的記錄位於： */var/opt/microsoft/omsagent/\<workspace id>/log/* 。新的 Linux 機器最多可能需要 30 分鐘才會出現在資訊安全中心中。

### <a name="onboard-your-windows-machines"></a>將您的 Windows 機器上線

若要新增 Windows 機器，您需要 [代理程式管理] 頁面上的資訊，且必須下載適當的代理程式檔案 (32/64 位元)。
1. 選取適用於您電腦處理器類型的 [下載 Windows 代理程式]  連結以下載安裝檔。
1. 從 [代理程式管理] 頁面，將 [工作區識別碼] 和 [主要金鑰] 複製到記事本中。
1. 將下載的安裝檔案複製到目標電腦，並加以執行。
1. 依照安裝精靈的指示操作 ([下一步]、[我同意]、[下一步]、[下一步])。
    1. 在 [Azure Log Analytics] 頁面上，貼上您在先前複製到「記事本」中的 [工作區識別碼] 和 [工作區金鑰 (主要金鑰)]。
    1. 如果電腦應該向 Azure Government Cloud 中的 Log Analytics 工作區回報，請從 [Azure 雲端] 下拉式清單中選取 [Azure US Gov]。
    1. 如果電腦需要透過 Proxy 伺服器與 Log Analytics 服務進行通訊，請選取 [進階]  ，然後提供 Proxy 伺服器的 URL 和連接埠號碼。
    1. 在輸入所有的組態設定後，選取 [下一步]。
    1. 在 [安裝準備就緒] 頁面上檢閱要套用的設定，然後選取 [安裝]。
    1. 在 [設定成功完成]  頁面上，選取 [完成]  。

完成時，[Microsoft 監視代理程式] 會出現在 [控制台] 中。 您可以在該處檢閱您的設定，並確認代理程式已連線。

如需安裝及設定代理程式的詳細資訊，請參閱[連線至 Windows 機器](../azure-monitor/platform/agent-windows.md#install-agent-using-setup-wizard)。

::: zone-end

## <a name="verifying"></a>驗證中

恭喜！ 現在，您已可在同一位置檢視您的 Azure 機器和非 Azure 機器。 開啟[資產清查](asset-inventory.md) 頁面，並篩選到相關的資源類型。 這些圖示會區分類型：

  ![非 Azure 機器的 ASC 圖示](./media/quick-onboard-linux-computer/security-center-monitoring-icon1.png) 非 Azure 機器

  ![Azure 機器的 ASC 圖示](./media/quick-onboard-linux-computer/security-center-monitoring-icon2.png) Azure VM

  ![Azure Arc 伺服器的 ASC 圖示](./media/quick-onboard-linux-computer/arc-enabled-machine-icon.png) 已啟用 Azure Arc 的伺服器

## <a name="next-steps"></a>後續步驟

此頁面說明了如何將您的非 Azure 機器新增至 Azure 資訊安全中心。 若要監視其狀態，請依照下列頁面的說明使用清查工具：

- [利用資產清查來探索和管理您的資源](asset-inventory.md)
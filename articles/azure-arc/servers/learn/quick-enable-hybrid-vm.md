---
title: 將混合式電腦與已啟用 Azure Arc 的伺服器連線 (預覽)
description: 了解如何使用已啟用 Azure Arc 的伺服器 (預覽) 連線和註冊您的混合式電腦。
ms.topic: quickstart
ms.date: 08/12/2020
ms.openlocfilehash: eacf75871b1f7cc7fc3b703d8859338578e43456
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88213613"
---
# <a name="quickstart-connect-hybrid-machine-with-azure-arc-enabled-servers-preview"></a>快速入門：將混合式電腦與已啟用 Azure Arc 的伺服器連線 (預覽)

[已啟用 Azure Arc 的伺服器](../overview.md) (預覽) 可讓您管理和控管裝載在內部部署、邊緣和多重雲端環境中的 Windows 和 Linux 電腦。 在本快速入門中，您將在裝載於 Azure 外部的 Windows 或 Linux 電腦上，部署和設定已連線的電腦代理程式，以透過啟用 Arc 的伺服器 (預覽) 進行管理。

## <a name="prerequisites"></a>必要條件

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 部署已啟用 Arc 的伺服器 (預覽) 混合式連線電腦代理程式需要您擁有電腦的管理員權限，才能安裝和設定代理程式。 在 Linux 上請使用根帳戶；在 Windows 上則必須使用本機管理員群組成員的帳戶。

* 開始之前，請務必檢閱代理程式[必要條件](../agent-overview.md#prerequisites)並確認下列事項：

    * 您的目標電腦應該執行支援的[作業系統](../agent-overview.md#supported-operating-systems)。

    * 您的帳戶已將指派授與[必要的 Azure 角色](../agent-overview.md#required-permissions)。

    * 如果電腦透過防火牆或 Proxy 伺服器連線到網際網路進行通訊，請確定[列出的 URL](../agent-overview.md#networking-configuration) 不會遭到封鎖。

    * 已啟用 Azure Arc 的伺服器 (預覽) 只支援[此處](../overview.md#supported-regions)所指定的區域。

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

## <a name="register-azure-resource-providers"></a>註冊 Azure 資源提供者

已啟用 Azure Arc 的伺服器 (預覽) 需依賴您訂用帳戶中的下列 Azure 資源提供者來使用此服務：

* Microsoft.HybridCompute
* Microsoft.GuestConfiguration

使用下列命令來註冊：

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

## <a name="generate-installation-script"></a>產生安裝指令碼

用來自動下載、安裝以及用來與 Azure Arc 建立連線的指令碼，可從 Azure 入口網站取得。 若要完成此程序，請執行下列動作：

1. 藉由按一下 [所有服務] 然後搜尋並選取 [電腦 - Azure Arc]，在 Azure 入口網站中啟動 Azure Arc 服務。

    :::image type="content" source="./media/quick-enable-hybrid-vm/search-machines.png" alt-text="搜尋所有服務中已啟用 Arc 的伺服器" border="false":::

1. 在 [機器 - Azure Arc] 頁面上，選取左上角的 [新增]，或選取中間窗格底部的 [建立機器 - Azure Arc] 選項。

1. 在 [選取方法] 頁面上，選取 [使用互動式指令碼新增機器] 圖格，然後選取 [產生指令碼]。

1. 在 [產生指令碼] 頁面上，選取您要在 Azure 中用來管理機器的訂用帳戶和資源群組。 選取要用來儲存機器中繼資料的 Azure 位置。

1. 在 [產生指令碼] 頁面的 [作業系統] 下拉式清單中，選取要作為指令碼執行平台的作業系統。

1. 如果機器透過 Proxy 伺服器進行通訊以連線到網際網路，請選取 [下一步：Proxy 伺服器]。

1. 在 [Proxy 伺服器] 索引標籤上，指定要供機器用來與 Proxy 伺服器通訊的 Proxy 伺服器 IP 位址或名稱與連接埠號碼。 請以 `http://<proxyURL>:<proxyport>` 格式輸入值。

1. 選取 [檢閱 + 產生]。

1. 在 [檢閱 + 產生] 索引標籤上檢閱摘要資訊，然後選取 [下載]。 如果您還需要變更，請選取 [上一步]。

## <a name="install-the-agent-using-the-script"></a>使用指令碼安裝代理程式

### <a name="windows-agent"></a>Windows 代理程式

1. 登入伺服器。

1. 開啟已提高權限的 64 位元 PowerShell 命令提示字元。

1. 變更至作為指令碼複製目的地的資料夾或共用，然後在伺服器上執行 `./OnboardingScript.ps1` 指令碼來執行此工作。

### <a name="linux-agent"></a>Linux 代理程式

1. 若要在可直接與 Azure 通訊的目標電腦上安裝 Linux 代理程式，請執行下列命令：

    ```bash
    bash ~/Install_linux_azcmagent.sh
    ```

    * 如果目標電腦透過 Proxy 伺服器進行通訊，請執行下列命令：

        ```bash
        bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
        ```

## <a name="verify-the-connection-with-azure-arc"></a>驗證與 Azure Arc 的連線

在安裝好代理程式並將其設定為連線至已啟用 Azure Arc 的伺服器 (預覽) 之後，請移至 Azure 入口網站以確認伺服器已成功連線。 在 [Azure 入口網站](https://aka.ms/hybridmachineportal)中檢視您的電腦。

:::image type="content" source="./media/quick-enable-hybrid-vm/enabled-machine.png" alt-text="電腦成功連線" border="false":::

## <a name="next-steps"></a>後續步驟

既然您已啟用 Linux 或 Windows 混合式電腦並成功連線到服務，就可以開始啟用 Azure 原則以了解 Azure 中的合規性。

若要了解如何識別未安裝 Log Analytics 代理程式的已啟用 Azure Arc 伺服器 (預覽) 的電腦，請繼續進行教學課程：

> [!div class="nextstepaction"]
> [建立原則指派以識別不相容資源](tutorial-assign-policy-portal.md)

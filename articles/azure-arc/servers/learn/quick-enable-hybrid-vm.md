---
title: 將混合式機器與已啟用 Azure Arc 的伺服器連線
description: 了解如何使用已啟用 Azure Arc 的伺服器連線和註冊您的混合式機器。
ms.topic: quickstart
ms.date: 12/15/2020
ms.openlocfilehash: 68869854cbfcf6d7297137e6239b2229a20c04a1
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516790"
---
# <a name="quickstart-connect-hybrid-machine-with-azure-arc-enabled-servers"></a>快速入門：將混合式機器與已啟用 Azure Arc 的伺服器連線

[已啟用 Azure Arc 的伺服器](../overview.md)可讓您管理和控管裝載在內部部署、邊緣和多重雲端環境中的 Windows 和 Linux 機器。 在本快速入門中，您將在裝載於 Azure 外部的 Windows 或 Linux 機器上，部署和設定連線機器代理程式，以透過啟用 Arc 的伺服器進行管理。

## <a name="prerequisites"></a>必要條件

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 要部署已啟用 Arc 的伺服器混合式連線機器代理程式，您必須擁有電腦的管理員權限，以安裝和設定代理程式。 在 Linux 上請使用根帳戶；在 Windows 上則必須使用本機管理員群組成員的帳戶。

* 開始之前，請務必檢閱代理程式[必要條件](../agent-overview.md#prerequisites)並確認下列事項：

    * 您的目標電腦應該執行支援的[作業系統](../agent-overview.md#supported-operating-systems)。

    * 您的帳戶已將指派授與[必要的 Azure 角色](../agent-overview.md#required-permissions)。

    * 如果電腦透過防火牆或 Proxy 伺服器連線到網際網路進行通訊，請確定[列出的 URL](../agent-overview.md#networking-configuration) 不會遭到封鎖。

    * 已啟用 Azure Arc 的伺服器僅支援[這裡](../overview.md#supported-regions)所指定的區域。

> [!WARNING]
> Linux 主機名稱或 Windows 電腦名稱不能在名稱中使用其中一個保留字或商標，否則嘗試向 Azure 註冊連線的機器將會失敗。 如需保留字的清單，請參閱[解決保留資源名稱的錯誤](../../../azure-resource-manager/templates/error-reserved-resource-name.md)。

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

## <a name="register-azure-resource-providers"></a>註冊 Azure 資源提供者

已啟用 Azure Arc 的伺服器需依賴您訂用帳戶中的下列 Azure 資源提供者來使用此服務：

* Microsoft.HybridCompute
* Microsoft.GuestConfiguration

使用下列命令來註冊：

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

## <a name="generate-installation-script"></a>產生安裝指令碼

用來自動下載、安裝以及用來與 Azure Arc 建立連線的指令碼，可從 Azure 入口網站取得。 若要完成此程序，請執行下列動作：

1. 按一下 [所有服務]，然後搜尋並選取 [伺服器 - Azure Arc]，藉以在 Azure 入口網站中啟動 Azure Arc 服務。

    :::image type="content" source="./media/quick-enable-hybrid-vm/search-machines.png" alt-text="搜尋所有服務中已啟用 Arc 的伺服器" border="false":::

1. 在 [伺服器 - Azure Arc] 頁面上，選取左上方的 [新增]。

1. 在 [選取方法] 頁面上，選取 [使用互動式指令碼新增伺服器] 圖格，然後選取 [產生指令碼]。

1. 在 [產生指令碼] 頁面上，選取您要在 Azure 中用來管理機器的訂用帳戶和資源群組。 選取要用來儲存機器中繼資料的 Azure 位置。 此位置可與資源群組的位置相同或不同。

1. 在 [必要條件] 頁面上檢閱資訊，然後選取 [下一步:資源詳細資料]。

1. 在 [資源詳細資料] 頁面上，提供下列各項：

    1. 在 [資源群組] 下拉式清單中，選取將受到管理的機器所屬的資源群組。
    1. 在 [區域] 下拉式清單中，選取要儲存伺服器中繼資料的 Azure 區域。
    1. 在 [作業系統] 下拉式清單中，選取要在其中設定並執行指令碼的作業系統。
    1. 如果機器是透過 Proxy 伺服器進行通訊以連線至網際網路，請指定 Proxy 伺服器 IP 位址或名稱，以及機器用來與 Proxy 伺服器通訊的連接埠號碼。 請以 `http://<proxyURL>:<proxyport>` 格式輸入值。
    1. 完成時，選取 [下一步:標籤]。

1. 在 [標籤] 頁面上，檢閱建議的預設 [實體位置標籤] 並輸入值，或指定一或多個 [自訂標籤] 以支援您的標準。

1. 完成時，選取 [下一步:下載並執行指令碼]。

1. 在 [下載並執行指令碼] 頁面上檢閱摘要資訊，然後選取 [下載]。 如果您還需要變更，請選取 [上一步]。

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

在安裝好代理程式並將其設定為連線至已啟用 Azure Arc 的伺服器之後，請移至 Azure 入口網站以確認伺服器已成功連線。 在 [Azure 入口網站](https://aka.ms/hybridmachineportal)中檢視您的電腦。

:::image type="content" source="./media/quick-enable-hybrid-vm/enabled-machine.png" alt-text="電腦成功連線" border="false":::

## <a name="next-steps"></a>後續步驟

既然您已啟用 Linux 或 Windows 混合式電腦並成功連線到服務，就可以開始啟用 Azure 原則以了解 Azure 中的合規性。

若要了解如何識別已啟用 Azure Arc 但未安裝 Log Analytics 代理程式的伺服器電腦，請繼續進行教學課程：

> [!div class="nextstepaction"]
> [建立原則指派以識別不相容資源](tutorial-assign-policy-portal.md)

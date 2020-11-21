---
title: 設定 Windows 虛擬桌面的 GPU - Azure
description: 如何在 Windows 虛擬桌面中啟用 GPU 加速的轉譯和編碼功能。
author: gundarev
ms.topic: how-to
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: c3a23276ce19f6d7b4cf341bac155ec84363fe5f
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018336"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop"></a>設定 Windows 虛擬桌面的圖形處理器 (GPU) 加速

>[!IMPORTANT]
>此內容適用於具有 Azure Resource Manager Windows 虛擬桌面物件的 Windows 虛擬桌面。 如果您使用不含 Azure Resource Manager 物件的 Windows 虛擬桌面 (傳統)，請參閱[此文章](./virtual-desktop-fall-2019/configure-vm-gpu-2019.md)。

Windows 虛擬桌面支援採用 GPU 加速的轉譯和編碼功能，可藉此改善應用程式效能和擴充性。 GPU 加速對於需要大量圖形的應用程式特別重要。

請遵循本文中的指示來建立 GPU 最佳化的 Azure 虛擬機器，並將其新增至您的主機集區，然後將其設定為使用 GPU 加速進行轉譯和編碼。 本文假設您已設定 Windows 虛擬桌面的租用戶。

## <a name="select-an-appropriate-gpu-optimized-azure-virtual-machine-size"></a>選取適當的 GPU 優化 Azure 虛擬機器大小

選取其中一個 Azure 的 [NV 系列](../virtual-machines/nv-series.md)、 [NVv3 系列](../virtual-machines/nvv3-series.md)或 [NVv4 系列](../virtual-machines/nvv4-series.md) VM 大小。 這些是針對應用程式和桌面虛擬化量身打造的，可讓應用程式和 Windows 使用者介面進行 GPU 加速。 主機集區的正確選擇取決於數個因素，包括您的特定應用程式工作負載、所需的使用者體驗品質和成本。 一般而言，更大且更強大的 Gpu 可在指定的使用者密度上提供更佳的使用者體驗，而較小和小數的 GPU 大小則可讓您更精確地控制成本和品質。

>[!NOTE]
>Azure 的 NC、NCv2、NCv3、ND 和 NDv2 系列 Vm 通常不適合 Windows 虛擬桌面工作階段主機。 這些 Vm 是專為特製化、高效能的計算或機器學習工具所量身打造，例如以 NVIDIA CUDA 建立的工具。 使用 NVIDIA Gpu 的一般應用程式和桌面加速需要 NVIDIA GRID 授權;這是由 Azure 針對建議的 VM 大小所提供，但需要針對 NC/ND 系列 Vm 分開排列。

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>建立主機集區、佈建您的虛擬機器，以及設定應用程式群組

使用您選取的 VM 大小建立新的主機集區。 如需指示，請參閱[教學課程：透過 Azure 入口網站建立主機集區](./create-host-pools-azure-marketplace.md)。

Windows 虛擬桌面支援在下列作業系統中使用 GPU 加速的轉譯和編碼功能：

* Windows 10 1511 版或更新版本
* Windows Server 2016 或更新版本

您也必須設定應用程式群組，或使用您建立新主機集區時，自動建立的預設桌面應用程式群組 (名為「桌面應用程式群組」)。 如需指示，請參閱[教學課程：管理 Windows 虛擬桌面的應用程式群組](./manage-app-groups.md)。

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>在您的虛擬機器中安裝支援的圖形驅動程式

若要在 Windows 虛擬桌面中使用 Azure N 系列 VM 的 GPU 功能，您必須安裝適當的圖形驅動程式。 請遵循[支援的作業系統和驅動程式](../virtual-machines/sizes-gpu.md#supported-operating-systems-and-drivers)中的指示，透過手動或使用 Azure VM 擴充功能的方式，從適當圖形廠商安裝驅動程式。

Windows 虛擬桌面僅支援由 Azure 散發的驅動程式。 針對具有 NVIDIA Gpu 的 Azure NV 系列 Vm，只有 [NVIDIA GRID 驅動程式](../virtual-machines/windows/n-series-driver-setup.md#nvidia-grid-drivers)，而非 nvidia TESLA (CUDA) 驅動程式，支援一般用途應用程式和桌上型電腦的 GPU 加速。

安裝驅動程式之後，必須重新啟動 VM。 使用上述指示中的驗證步驟，確認已成功安裝圖形驅動程式。

## <a name="configure-gpu-accelerated-app-rendering"></a>設定 GPU 加速的應用程式轉譯

根據預設，在多工作階段組態中執行的應用程式和桌面會使用 CPU 進行轉譯，而不會利用可用的 GPU 進行轉譯。 您可以設定工作階段主機的群組原則來啟用 GPU 加速的轉譯：

1. 使用具有本機系統管理員權限的帳戶連線到 VM 桌面。
2. 開啟 [開始] 功能表，然後輸入 "gpedit.msc" 以開啟 [群組原則編輯器]。
3. 瀏覽樹狀結構至 [電腦設定] > [系統管理範本] > [Windows 元件] > [遠端桌面服務] > [遠端桌面工作階段主機] > [遠端工作階段環境]。
4. 選取 [原則 **使用硬體圖形配接器進行所有遠端桌面服務會話** ]，並將此原則設為 [ **啟用** ]，以在遠端會話中啟用 GPU 轉譯。

## <a name="configure-gpu-accelerated-frame-encoding"></a>設定 GPU 加速的畫面編碼

遠端桌面會針對應用程式和桌上型電腦所轉譯的所有圖形進行編碼 (無論以 GPU 或 CPU 轉譯)，以傳輸到遠端桌面用戶端。 當畫面的一部分經常更新時，畫面的這個部分會以 (h.264/AVC) 的影片編解碼器編碼。 根據預設，遠端桌面不會利用可用的 GPU 來進行此編碼。 您可以設定工作階段主機的群組原則來啟用 GPU 加速的畫面編碼。 繼續上述步驟：

>[!NOTE]
>NVv4 系列 Vm 中無法使用 GPU 加速的畫面編碼。

1. 選取 [針對遠端桌面連線設定 H.264/AVC 硬體編碼的優先權] 原則，並將此原則設定為 [啟用]，以在遠端工作階段中啟用 AVC/H.264 的硬體編碼。

    >[!NOTE]
    >在 Windows Server 2016 中，將 [優先使用 AVC 硬體編碼] 選項設定為 [一律嘗試]。

2. 編輯群組原則之後，您就可以強制執行群組原則更新。 開啟命令提示字元，然後輸入：

    ```cmd
    gpupdate.exe /force
    ```

3. 登出遠端桌面工作階段。

## <a name="configure-fullscreen-video-encoding"></a>設定全螢幕影片編碼

如果您經常使用產生高框架速率內容的應用程式（例如3D 模型、CAD/攝影機和影片應用程式），您可以選擇啟用遠端會話的全螢幕影片編碼。 全螢幕視頻設定檔可為這類應用程式提供更高的畫面播放速率和更好的使用者體驗，同時也會花費網路頻寬和工作階段主機和用戶端資源。 建議使用 GPU 加速的框架編碼來進行全螢幕的影片編碼。 設定工作階段主機的群組原則，以啟用全螢幕的影片編碼。 繼續上述步驟：

1. 選取 [針對遠端桌面連線設定 H.264/AVC 444 圖形模式的優先權] 原則，並將此原則設定為 [啟用]，以在遠端工作階段中強制執行 H.264/AVC 444 轉碼器。
2. 編輯群組原則之後，您就可以強制執行群組原則更新。 開啟命令提示字元，然後輸入：

    ```cmd
    gpupdate.exe /force
    ```

3. 登出遠端桌面工作階段。
## <a name="verify-gpu-accelerated-app-rendering"></a>驗證 GPU 加速的應用程式轉譯

若要驗證應用程式是否使用 GPU 進行轉譯，請嘗試下列任一方式：

* 針對具有 NVIDIA GPU 的 Azure Vm，請使用 `nvidia-smi` 在執行應用程式時， [確認驅動程式安裝](../virtual-machines/windows/n-series-driver-setup.md#verify-driver-installation) 中所述的公用程式來檢查 GPU 使用量。
* 在支援的作業系統版本上，您可以使用工作管理員來檢查 GPU 使用率。 在 [效能] 索引標籤中選取 GPU，即可查看應用程式是否正在利用 GPU。

## <a name="verify-gpu-accelerated-frame-encoding"></a>驗證 GPU 加速的畫面編碼

若要驗證遠端桌面是否正在使用 GPU 加速編碼：

1. 使用 Windows 虛擬桌面用戶端連線到 VM 桌面。
2. 啟動事件檢視器並瀏覽至下列節點：[應用程式和服務記錄檔] > [Microsoft] > [Windows] > [RemoteDesktopServices-RdpCoreCDV][Operational] > 
3. 若要判斷是否使用 GPU 加速編碼，請尋找事件識別碼 170。 如果您看到「已啟用 AVC 硬體編碼器：1」，則表示已使用 GPU 編碼。

## <a name="verify-fullscreen-video-encoding"></a>驗證全螢幕影片編碼

若要驗證遠端桌面是否使用全螢幕的影片編碼：

1. 使用 Windows 虛擬桌面用戶端連線到 VM 桌面。
2. 啟動事件檢視器並瀏覽至下列節點：[應用程式和服務記錄檔] > [Microsoft] > [Windows] > [RemoteDesktopServices-RdpCoreCDV][Operational] > 
3. 若要判斷是否使用全螢幕影片編碼，請尋找事件識別碼162。 如果您看到「AVC 可供使用：1 個初始設定檔：2048」，則表示使用 AVC 444。

## <a name="next-steps"></a>後續步驟

這些指示應該可讓您能夠在一個工作階段主機 (一個 VM) 上啟動並執行 GPU 加速。 在較大的主機集區上啟用 GPU 加速有一些額外考量：

* 請考慮使用 [VM 擴充功能](../virtual-machines/extensions/overview.md)來簡化多部 VM 的驅動程式安裝和更新。 針對具有 NVIDIA GPU 的 VM，應使用 [NVIDIA GPU 驅動程式擴充功能](../virtual-machines/extensions/hpccompute-gpu-windows.md)，而具有 AMD GPU 的 VM 應使用 [AMD GPU 驅動程式擴充功能](../virtual-machines/extensions/hpccompute-amd-gpu-windows.md)。
* 請考慮使用 Active Directory 群組原則來簡化多個 VM 上的群組原則設定。 如需在 Active Directory 網域中部署群組原則的詳細資訊，請參閱[使用群組原則物件](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731212(v=ws.11))。
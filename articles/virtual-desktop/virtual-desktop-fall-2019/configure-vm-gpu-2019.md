---
title: 設定 Windows 虛擬桌面 (傳統) 的 GPU-Azure
description: 如何在 Windows 虛擬桌面 (傳統) 中啟用 GPU 加速轉譯和編碼。
author: gundarev
ms.topic: how-to
ms.date: 03/30/2020
ms.author: denisgun
ms.openlocfilehash: 32d5c280e80b2f21b30bb34a182070da51e21026
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "88008486"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop-classic"></a>為 Windows 虛擬桌面 (傳統) 設定圖形處理單元 (GPU) 加速

>[!IMPORTANT]
>此內容適用於不支援 Azure Resource Manager Windows 虛擬桌面物件的 Windows 虛擬桌面 (傳統)。 如果您嘗試管理 Azure Resource Manager Windows 虛擬桌面物件，請參閱[這篇文章](../configure-vm-gpu.md)。

Windows 虛擬桌面支援採用 GPU 加速的轉譯和編碼功能，可藉此改善應用程式效能和擴充性。 GPU 加速對於需要大量圖形的應用程式特別重要。

請遵循本文中的指示來建立 GPU 最佳化的 Azure 虛擬機器，並將其新增至您的主機集區，然後將其設定為使用 GPU 加速進行轉譯和編碼。 本文假設您已設定 Windows 虛擬桌面的租用戶。

## <a name="select-a-gpu-optimized-azure-virtual-machine-size"></a>選取 GPU 最佳化的 Azure 虛擬機器大小

Azure 提供數個 [GPU 最佳化的虛擬機器大小](/azure/virtual-machines/windows/sizes-gpu)。 主機集區的正確選擇取決於數個因素，包括您的特定應用程式工作負載、所需的使用者體驗品質和成本。 一般而言，較大且能力較強的 GPU 可在指定的使用者密度上提供更好的使用者體驗。

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>建立主機集區、佈建您的虛擬機器，以及設定應用程式群組

使用您選取的 VM 大小建立新的主機集區。 如需指示，請參閱[教學課程：使用 Azure Marketplace 建立主機集](/azure/virtual-desktop/create-host-pools-azure-marketplace)區。

Windows 虛擬桌面支援在下列作業系統中使用 GPU 加速的轉譯和編碼功能：

* Windows 10 1511 版或更新版本
* Windows Server 2016 或更新版本

您也必須設定應用程式群組，或使用您建立新主機集區時，自動建立的預設桌面應用程式群組 (名為「桌面應用程式群組」)。 如需指示，請參閱[教學課程：管理 Windows 虛擬桌面的應用程式群組](/azure/virtual-desktop/manage-app-groups)。

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>在您的虛擬機器中安裝支援的圖形驅動程式

若要在 Windows 虛擬桌面中使用 Azure N 系列 VM 的 GPU 功能，您必須安裝適當的圖形驅動程式。 請遵循[支援的作業系統和驅動程式](/azure/virtual-machines/windows/sizes-gpu#supported-operating-systems-and-drivers)中的指示，透過手動或使用 Azure VM 擴充功能的方式，從適當圖形廠商安裝驅動程式。

Windows 虛擬桌面僅支援由 Azure 散發的驅動程式。 Additionaly，針對具有 NVIDIA Gpu 的 Azure Vm，只有[NVIDIA GRID 驅動程式](/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers)支援 Windows 虛擬桌面。

安裝驅動程式之後，必須重新啟動 VM。 使用上述指示中的驗證步驟，確認已成功安裝圖形驅動程式。

## <a name="configure-gpu-accelerated-app-rendering"></a>設定 GPU 加速的應用程式轉譯

根據預設，在多工作階段組態中執行的應用程式和桌面會使用 CPU 進行轉譯，而不會利用可用的 GPU 進行轉譯。 您可以設定工作階段主機的群組原則來啟用 GPU 加速的轉譯：

1. 使用具有本機系統管理員權限的帳戶連線到 VM 桌面。
2. 開啟 [開始] 功能表，然後輸入 "gpedit.msc" 以開啟 [群組原則編輯器]。
3. 瀏覽樹狀結構至 [電腦設定] > [系統管理範本] > [Windows 元件] > [遠端桌面服務] > [遠端桌面工作階段主機] > [遠端工作階段環境]。
4. 選取 [針對所有遠端桌面服務工作階段使用硬體預設圖形卡] 原則，並將此原則設定為 [啟用]，以在遠端工作階段中啟用 GPU 轉譯。

## <a name="configure-gpu-accelerated-frame-encoding"></a>設定 GPU 加速的畫面編碼

遠端桌面會針對應用程式和桌上型電腦所轉譯的所有圖形進行編碼 (無論以 GPU 或 CPU 轉譯)，以傳輸到遠端桌面用戶端。 根據預設，遠端桌面不會利用可用的 GPU 來進行此編碼。 您可以設定工作階段主機的群組原則來啟用 GPU 加速的畫面編碼。 繼續上述步驟：

1. 選取 [針對遠端桌面連線設定 H.264/AVC 444 圖形模式的優先權] 原則，並將此原則設定為 [啟用]，以在遠端工作階段中強制執行 H.264/AVC 444 轉碼器。
2. 選取 [針對遠端桌面連線設定 H.264/AVC 硬體編碼的優先權] 原則，並將此原則設定為 [啟用]，以在遠端工作階段中啟用 AVC/H.264 的硬體編碼。

    >[!NOTE]
    >在 Windows Server 2016 中，將 [優先使用 AVC 硬體編碼] 選項設定為 [一律嘗試]。

3. 編輯群組原則之後，您就可以強制執行群組原則更新。 開啟命令提示字元，然後輸入：

    ```batch
    gpupdate.exe /force
    ```

4. 登出遠端桌面工作階段。

## <a name="verify-gpu-accelerated-app-rendering"></a>驗證 GPU 加速的應用程式轉譯

若要驗證應用程式是否使用 GPU 進行轉譯，請嘗試下列任一方式：

* 針對具有 NVIDIA GPU 的 Azure VM，請依照[驗證驅動程式安裝](/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation)中所述的方式，使用 `nvidia-smi` 公用程式在執行應用程式時檢查 GPU 使用率。
* 在支援的作業系統版本上，您可以使用工作管理員來檢查 GPU 使用率。 在 [效能] 索引標籤中選取 GPU，即可查看應用程式是否正在利用 GPU。

## <a name="verify-gpu-accelerated-frame-encoding"></a>驗證 GPU 加速的畫面編碼

若要驗證遠端桌面是否正在使用 GPU 加速編碼：

1. 使用 Windows 虛擬桌面用戶端連線到 VM 桌面。
2. 啟動事件檢視器並瀏覽至下列節點：[應用程式和服務記錄檔] > [Microsoft] > [Windows] > [RemoteDesktopServices-RdpCoreCDV][Operational] > 
3. 若要判斷是否使用 GPU 加速編碼，請尋找事件識別碼 170。 如果您看到「已啟用 AVC 硬體編碼器：1」，則表示已使用 GPU 編碼。
4. 若要判斷是否使用 AVC 444 模式，請尋找事件識別碼 162。 如果您看到「AVC 可供使用：1 個初始設定檔：2048」，則表示使用 AVC 444。

## <a name="next-steps"></a>後續步驟

這些指示應該可讓您能夠在一個工作階段主機 (一個 VM) 上啟動並執行 GPU 加速。 在較大的主機集區上啟用 GPU 加速有一些額外考量：

* 請考慮使用 [VM 擴充功能](/azure/virtual-machines/extensions/overview)來簡化多部 VM 的驅動程式安裝和更新。 針對具有 NVIDIA GPU 的 VM，應使用 [NVIDIA GPU 驅動程式擴充功能](/azure/virtual-machines/extensions/hpccompute-gpu-windows)，而具有 AMD GPU 的 VM 應使用 AMD GPU 驅動程式擴充功能。
* 請考慮使用 Active Directory 群組原則來簡化多個 VM 上的群組原則設定。 如需在 Active Directory 網域中部署群組原則的詳細資訊，請參閱[使用群組原則物件](https://go.microsoft.com/fwlink/p/?LinkId=620889)。
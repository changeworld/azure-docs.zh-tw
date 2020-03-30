---
title: 為 Windows 虛擬桌面配置 GPU - Azure
description: 如何在 Windows 虛擬桌面中啟用 GPU 加速呈現和編碼。
services: virtual-desktop
author: gundarev
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: 941a1ff23668a3202028e8b693b57d902095b3b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78384817"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop"></a>為 Windows 虛擬桌面配置圖形處理單元 （GPU） 加速

Windows 虛擬桌面支援 GPU 加速呈現和編碼，以提高應用性能和可伸縮性。 GPU 加速對於圖形密集型應用尤為重要。

按照本文中的說明創建 GPU 優化的 Azure 虛擬機器，將其添加到主機池，並將其配置為使用 GPU 加速進行渲染和編碼。 本文假定您已經配置了 Windows 虛擬桌面租戶。

## <a name="select-a-gpu-optimized-azure-virtual-machine-size"></a>選擇 GPU 優化的 Azure 虛擬機器大小

Azure 提供了許多[GPU 優化的虛擬機器大小](/azure/virtual-machines/windows/sizes-gpu)。 主機池的正確選擇取決於許多因素，包括您的特定應用工作負載、所需的使用者體驗品質和成本。 通常，更大、功能更強的 GPU 在給定使用者密度下提供更好的使用者體驗。

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>創建主機池、預配虛擬機器和配置應用組

使用所選大小的 VM 創建新的主機池。 有關說明，請參閱[教程：使用 Azure 應用商店創建主機池](/azure/virtual-desktop/create-host-pools-azure-marketplace)。

Windows 虛擬桌面支援 GPU 加速呈現和編碼在以下作業系統中：

* Windows 10 版本 1511 或更新
* Windows Server 2016 或更新版本

您還必須配置應用組，或使用在創建新主機池時自動創建的預設桌面應用組（名為"桌面應用程式組"）。 有關說明，請參閱[教程：管理 Windows 虛擬桌面的應用組](/azure/virtual-desktop/manage-app-groups)。

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>在虛擬機器中安裝受支援的圖形驅動程式

要利用 Windows 虛擬桌面中的 Azure N 系列 VM 的 GPU 功能，必須安裝相應的圖形驅動程式。 按照[支援作業系統和驅動程式](/azure/virtual-machines/windows/sizes-gpu#supported-operating-systems-and-drivers)的說明，手動或使用 Azure VM 擴展安裝來自相應圖形供應商的驅動程式。

只有 Azure 分發的驅動程式支援 Windows 虛擬桌面。 附加功能，對於具有 NVIDIA GPU 的 Azure VM，Windows 虛擬桌面僅支援[NVIDIA 網格驅動程式](/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers)。

驅動程式安裝後，需要重新開機 VM。 使用上述說明中的驗證步驟確認已成功安裝圖形驅動程式。

## <a name="configure-gpu-accelerated-app-rendering"></a>配置 GPU 加速應用渲染

預設情況下，以多會話配置運行的應用和桌面使用 CPU 呈現，並且不利用可用的 GPU 進行呈現。 為工作階段主機配置群組原則以啟用 GPU 加速呈現：

1. 使用具有本地管理員許可權的帳戶連接到 VM 的桌面。
2. 打開"開始"功能表並鍵入"gpedit.msc"以打開群組原則編輯器。
3. 將樹導航到**電腦配置** > **管理範本** > **Windows 元件** > **遠端桌面服務** > **遠端桌面工作階段主機** > **遠端會話環境**。
4. 選擇策略**使用所有遠端桌面服務會話的硬體預設圖形配接器**，並將此策略設置為 **"已啟用"，** 以便在遠端會話中啟用 GPU 呈現。

## <a name="configure-gpu-accelerated-frame-encoding"></a>配置 GPU 加速幀編碼

遠端桌面對應用和桌面呈現的所有圖形（無論是使用 GPU 渲染還是 CPU）進行編碼，以便傳輸到遠端桌面用戶端。 預設情況下，遠端桌面不會利用可用於此編碼的可用 GPU。 為工作階段主機配置群組原則以啟用 GPU 加速幀編碼。 繼續上述步驟：

1. 為**遠端桌面連線選擇"H.264/AVC 444 圖形模式"策略優先順序**，並將此策略設置為**啟用**以強制遠端會話中的 H.264/AVC 444 編解碼器。
2. 選擇策略**為遠端桌面連線配置 H.264/AVC 硬體編碼**，並將此策略設置為 **"已啟用"，** 以便在遠端會話中啟用 AVC/H.264 的硬體編碼。

    >[!NOTE]
    >在 Windows 伺服器 2016 中，設置選項 **"首選 AVC 硬體編碼**"以**始終嘗試**。

3. 現在，群組原則已過編輯，請強制進行群組原則更新。 打開命令提示符並鍵入：

    ```batch
    gpupdate.exe /force
    ```

4. 從遠端桌面會話登出。

## <a name="verify-gpu-accelerated-app-rendering"></a>驗證 GPU 加速應用渲染

要驗證應用是否使用 GPU 進行渲染，請嘗試以下任一操作：

* 對於具有 NVIDIA GPU 的 Azure `nvidia-smi` VM，請使用["驗證驅動程式安裝](/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation)"中所述的實用程式，以檢查運行應用時的 GPU 利用率。
* 在支援的作業系統版本上，可以使用工作管理員檢查 GPU 利用率。 在"性能"選項卡中選擇 GPU 以查看應用是否正在使用 GPU。

## <a name="verify-gpu-accelerated-frame-encoding"></a>驗證 GPU 加速幀編碼

要驗證遠端桌面是否使用 GPU 加速編碼，

1. 使用 Windows 虛擬桌面用戶端連接到 VM 的桌面。
2. 啟動事件檢視器並導航到以下節點：**應用程式和服務日誌** > **微軟** > **Windows** > **遠端桌面服務-RdpCoreCDV** > **操作**
3. 要確定是否使用 GPU 加速編碼，請查找事件 ID 170。 如果您看到"AVC 硬體編碼器已啟用：1"，則使用 GPU 編碼。
4. 要確定是否使用 AVC 444 模式，請查找事件 ID 162。 如果您看到"AVC 可用：1 初始設定檔：2048"，則使用 AVC 444。

## <a name="next-steps"></a>後續步驟

這些說明應該讓您在單個工作階段主機 VM 上使用 GPU 加速啟動和運行。 跨較大主機池啟用 GPU 加速的一些其他注意事項：

* 請考慮使用[VM 擴展](/azure/virtual-machines/extensions/overview)來簡化跨多個 VM 的驅動程式安裝和更新。 使用[NVIDIA GPU 驅動程式擴展](/azure/virtual-machines/extensions/hpccompute-gpu-windows)功能，用於具有 NVIDIA GPU 的 VM，並使用 AMD GPU 驅動程式擴展（即將推出）用於具有 AMD GPU 的 VM。
* 請考慮使用 Active 目錄群組原則來簡化多個 VM 的群組原則配置。 有關在活動目錄域中部署群組原則的資訊，請參閱[使用群組原則物件](https://go.microsoft.com/fwlink/p/?LinkId=620889)。

---
title: 設定 Windows 虛擬桌面的 GPU-Azure
description: 如何在 Windows 虛擬桌面中啟用 GPU 加速轉譯和編碼。
services: virtual-desktop
author: gundarev
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: 64e8fab3ac352c906cfb63cd39f89acda4109b18
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719750"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop"></a>設定 Windows 虛擬桌面的圖形處理器（GPU）加速

Windows 虛擬桌面支援 GPU 加速轉譯和編碼，以改善應用程式效能和擴充性。 GPU 加速對於需要大量圖形的應用程式特別重要。

請遵循本文中的指示來建立 GPU 優化的 Azure 虛擬機器、將它新增至您的主機集區，並將其設定為使用 GPU 加速進行轉譯和編碼。 本文假設您已設定 Windows 虛擬桌面的租使用者。

## <a name="select-a-gpu-optimized-azure-virtual-machine-size"></a>選取 GPU 優化的 Azure 虛擬機器大小

Azure 提供一些[GPU 優化的虛擬機器大小](/azure/virtual-machines/windows/sizes-gpu)。 主機集區的正確選擇取決於數個因素，包括您的特定應用程式工作負載、所需的使用者體驗品質和成本。 一般而言，較大且更有能力的 Gpu 可在指定的使用者密度上提供更好的使用者體驗。

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>建立主機集區、布建您的虛擬機器，以及設定應用程式群組

使用您所選大小的 VM，建立新的主機集區。 如需指示，請參閱[教學課程：使用 Azure Marketplace 建立主機集](/azure/virtual-desktop/create-host-pools-azure-marketplace)區。

Windows 虛擬桌面支援下列作業系統中的 GPU 加速轉譯和編碼：

* Windows 10 1511 版或更新版本
* Windows Server 2016 或更新版本

您也必須設定應用程式群組，或使用當您建立新的主機集區時，自動建立的預設桌面應用程式群組（名為「桌面應用程式群組」）。 如需指示，請參閱[教學課程：管理 Windows 虛擬桌面的應用程式群組](/azure/virtual-desktop/manage-app-groups)。

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>在您的虛擬機器中安裝支援的圖形驅動程式

若要在 Windows 虛擬桌面中利用 Azure N 系列 Vm 的 GPU 功能，您必須安裝適當的圖形驅動程式。 遵循[支援的作業系統和驅動程式](/azure/virtual-machines/windows/sizes-gpu#supported-operating-systems-and-drivers)上的指示，以手動方式或使用 Azure VM 擴充功能，從適當的圖形廠商安裝驅動程式。

Windows 虛擬桌面僅支援由 Azure 散發的驅動程式。 Additionaly，針對具有 NVIDIA Gpu 的 Azure Vm，只有[NVIDIA GRID 驅動程式](/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers)支援 Windows 虛擬桌面。

安裝驅動程式之後，需要重新開機 VM。 使用上述指示中的驗證步驟，確認已成功安裝圖形驅動程式。

## <a name="configure-gpu-accelerated-app-rendering"></a>設定 GPU 加速應用程式轉譯

根據預設，在多會話設定中執行的應用程式和桌面會隨著 CPU 轉譯，而且不會利用可用的 Gpu 進行轉譯。 設定工作階段主機的群組原則，以啟用 GPU 加速轉譯：

1. 使用具有本機系統管理員許可權的帳戶連接到 VM 的桌面。
2. 開啟 [開始] 功能表，然後輸入 "gpedit.msc" 以開啟 [群組原則編輯器]。
3. 流覽樹狀結構至**電腦**設定 > **系統管理範本** > **Windows 元件** > **遠端桌面服務** > **遠端桌面工作階段主機** > **遠端會話環境**。
4. 選取 [原則] **[使用所有遠端桌面服務會話的硬體預設圖形配接器**]，並將此原則設定為 [**啟用**]，以在遠端會話中啟用 GPU 轉譯。

## <a name="configure-gpu-accelerated-frame-encoding"></a>設定 GPU 加速框架編碼

遠端桌面會將應用程式和桌上型電腦（無論以 GPU 或 CPU 轉譯）所轉譯的所有圖形編碼，以傳輸到遠端桌面用戶端。 根據預設，遠端桌面不會利用可用的 Gpu 來進行此編碼。 設定工作階段主機的群組原則，以啟用 GPU 加速的框架編碼。 繼續上述步驟：

1. **針對遠端桌面連線選取原則優先順序： h.264/AVC 444 圖形模式**，並將此原則設定為 [**啟用**]，以在遠端會話中強制執行 h.264/AVC 444 編解碼器。
2. 選取 [原則] [設定**遠端桌面連線的 h.264/AVC 硬體編碼**]，並將此原則設為 [**啟用**]，以在遠端會話中啟用 AVC/h.264 的硬體編碼。

    >[!NOTE]
    >在 Windows Server 2016 中，將選項 [**偏好 AVC 硬體編碼**] 設定為 [**一律嘗試**]。

3. 現在已編輯過群組原則，強制執行群組原則更新。 開啟命令提示字元，然後輸入：

    ```batch
    gpupdate.exe /force
    ```

4. 登出遠端桌面會話。

## <a name="verify-gpu-accelerated-app-rendering"></a>確認 GPU 加速應用程式轉譯

若要驗證應用程式是否使用 GPU 進行轉譯，請嘗試下列任一項：

* 針對具有 NVIDIA GPU 的 Azure Vm，請依照[確認驅動程式安裝](/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation)中所述，使用 `nvidia-smi` 公用程式，以在執行應用程式時檢查 GPU 使用率。
* 在支援的作業系統版本上，您可以使用工作管理員來檢查 GPU 使用率。 在 [效能] 索引標籤中選取 GPU，以查看應用程式是否正在利用 GPU。

## <a name="verify-gpu-accelerated-frame-encoding"></a>確認 GPU 加速畫面編碼

若要確認遠端桌面使用的是 GPU 加速編碼：

1. 使用 Windows 虛擬桌面用戶端連接到 VM 的桌面。
2. 啟動事件檢視器並流覽至下列節點： [**應用程式和服務記錄**檔] > **Microsoft** > **Windows** > **RemoteDesktopServices-RdpCoreTS** > **操作**
3. 若要判斷是否使用 GPU 加速編碼，請尋找事件識別碼170。 如果您看到「已啟用 AVC 硬體編碼器：1」，則會使用 GPU 編碼。
4. 若要判斷是否使用 AVC 444 模式，請尋找事件識別碼162。 如果您看到「可用的 AVC：1個初始設定檔：2048」，則會使用 AVC 444。

## <a name="next-steps"></a>後續步驟

這些指示應該讓您能夠在單一工作階段主機 VM 上啟動並執行 GPU 加速。 在較大的主機集區上啟用 GPU 加速的一些額外考慮：

* 請考慮使用[VM 擴充](/azure/virtual-machines/extensions/overview)功能來簡化多部 vm 的驅動程式安裝和更新。 針對具有 NVIDIA Gpu 的 Vm 使用[NVIDIA Gpu 驅動程式擴充](/azure/virtual-machines/extensions/hpccompute-gpu-windows)功能，並針對具有 amd Gpu 的 VM 使用 Amd Gpu 驅動程式擴充功能（即將推出）。
* 請考慮使用 Active Directory 群組原則，以簡化多個 Vm 的群組原則設定。 如需在 Active Directory 網域中部署群組原則的詳細資訊，請參閱[使用群組原則物件](https://go.microsoft.com/fwlink/p/?LinkId=620889)。

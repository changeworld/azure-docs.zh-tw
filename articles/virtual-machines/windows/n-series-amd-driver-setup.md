---
title: 適用于 Windows 的 Azure N 系列 AMD GPU 驅動程式設定
description: 如何針對在 Azure 中執行 Windows Server 或 Windows 的 N 系列 Vm 設定 AMD GPU 驅動程式
author: vikancha
manager: jkabat
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: 1bcc13db3f503c80fda71a2104d0ff8d99e67df6
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198003"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>在執行 Windows 的 N 系列 Vm 上安裝 AMD GPU 驅動程式

若要利用執行 Windows 的新 Azure NVv4 系列 Vm 的 GPU 功能，必須安裝 AMD GPU 驅動程式。 [AMD Gpu 驅動程式擴充](../extensions/hpccompute-amd-gpu-windows.md)功能會在 NVV4 系列 VM 上安裝 AMD gpu 驅動程式。 使用 Azure 入口網站或者 Azure PowerShell 或 Azure Resource Manager 範本之類的工具，安裝或管理擴充功能。 如需支援的作業系統和部署步驟，請參閱[AMD GPU 驅動程式擴充功能檔](../extensions/hpccompute-amd-gpu-windows.md)。

如果您選擇手動安裝 AMD GPU 驅動程式，本文提供支援的作業系統、驅動程式，以及安裝和驗證步驟。

NVv4 Vm 只支援 Microsoft 所發佈的 GPU 驅動程式。 請不要從任何其他來源安裝 GPU 驅動程式。

如需基本規格、儲存體容量與磁碟的詳細資料，請參閱 [GPU Windows VM 大小](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。



## <a name="supported-operating-systems-and-drivers"></a>支援的作業系統和驅動程式

| OS | 驅動程式 |
| -------- |------------- |
| Windows 10 EVD-組建1903 <br/><br/>Windows 10-組建1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20. q 1.1](https://download.microsoft.com/download/3/8/9/3893407b-e8aa-4079-8592-735d7dd1c19a/Radeon-Pro-Software-for-Enterprise-GA.exe) （.exe） |


## <a name="driver-installation"></a>驅動程式安裝

1. 透過遠端桌面連線到每個 NVv4 系列 VM。

2. 如果您是 NVv4 preview 客戶，請停止 VM 並等候它進入已停止（已解除配置）狀態。

3. 請啟動 VM，並下載最新的[AMD 清理公用程式](https://download.microsoft.com/download/4/f/1/4f19b714-9304-410f-9c64-826404e07857/AMDCleanupUtilityni.exe)。 執行 "amdcleanuputility-x64" 來卸載現有的驅動程式。 請不要使用與先前的驅動程式一起安裝的任何現有清理公用程式。  

4. 下載並安裝最新的驅動程式。

5. 重新啟動 VM。

## <a name="verify-driver-installation"></a>確認驅動程式安裝

您可以在 [裝置管理員] 中確認驅動程式安裝。 下列範例示範如何在 Azure NVv4 VM 上成功設定 Radeon 直覺 MI25 卡。
<br />
![GPU 驅動程式屬性](./media/n-series-amd-driver-setup/device-manager.png)

您可以使用 dxdiag 來驗證 GPU 顯示內容，包括視頻 RAM。 下列範例顯示 Azure NVv4 VM 上 Radeon 直覺 MI25 卡的1/2 磁碟分割。
<br />
![GPU 驅動程式屬性](./media/n-series-amd-driver-setup/dxdiag-output.png)

如果您執行的是 Windows 10 組建1903或更新版本，則 dxdiag 不會在 [顯示] 索引標籤中顯示任何資訊。請使用底部的 [儲存所有資訊] 選項，輸出檔案將會顯示與 AMD MI25 GPU 相關的資訊。

![GPU 驅動程式屬性](./media/n-series-amd-driver-setup/dxdiag-details.png)



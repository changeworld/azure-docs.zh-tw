---
title: 適用于 Windows 的 Azure N 系列 AMD GPU 驅動程式設定
description: 如何針對在 Azure 中執行 Windows Server 或 Windows 的 N 系列 Vm 設定 AMD GPU 驅動程式
services: virtual-machines-windows
author: vikancha
manager: jkabat
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: 164f07f6545c1c225814958bba5722536b11a9b4
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2020
ms.locfileid: "78269426"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>在執行 Windows 的 N 系列 Vm 上安裝 AMD GPU 驅動程式

若要利用執行 Windows 的新 Azure NVv4 系列 Vm 的 GPU 功能，必須安裝 AMD GPU 驅動程式。 未來幾周內將會提供 AMD 驅動程式擴充功能。 本文提供支援的作業系統、驅動程式，以及手動安裝和驗證步驟。

如需基本規格、儲存體容量與磁碟的詳細資料，請參閱 [GPU Windows VM 大小](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。



## <a name="supported-operating-systems-and-drivers"></a>支援的作業系統和驅動程式

| OS | 驅動程式 |
| -------- |------------- |
| Windows 10 EVD-組建1903 <br/><br/>Windows 10-組建1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20. q 1.1](https://download.microsoft.com/download/3/8/9/3893407b-e8aa-4079-8592-735d7dd1c19a/Radeon-Pro-Software-for-Enterprise-GA.exe) （.exe） |


## <a name="driver-installation"></a>驅動程式安裝

1. 透過遠端桌面連線到每個 NVv4 系列 VM。

2. 如果您是 NVv4 preview 客戶，請停止 VM 並等候它進入已停止（已解除配置）狀態。

3. 請啟動 VM，然後執行位於 ". ..\AMDCleanUninstallUtility" 資料夾中的 "amdcleanuputility-x64" 來卸載預覽驅動程式。 確切的路徑會根據先前的驅動程式安裝檔案的位置而有所不同。  

4. 下載並安裝最新的驅動程式。

5. 重新啟動 VM。

## <a name="verify-driver-installation"></a>確認驅動程式安裝

您可以在 [裝置管理員] 中確認驅動程式安裝。 下列範例示範如何在 Azure NVv4 VM 上成功設定 Radeon 直覺 MI25 卡。
<br />
![GPU 驅動程式屬性](./media/n-series-amd-driver-setup/device-manager.png)

您可以使用 dxdiag 來驗證 GPU 顯示內容，包括視頻 RAM。 下列範例顯示 Azure NVv4 VM 上 Radeon 直覺 MI25 卡的 1/8 資料分割。
<br />
![GPU 驅動程式屬性](./media/n-series-amd-driver-setup/dxdiag.png)

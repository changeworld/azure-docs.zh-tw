---
title: 適用于 Windows 的 Azure N 系列 AMD GPU 驅動程式設置
description: 如何為在 Azure 中運行 Windows 伺服器或 Windows 的 N 系列 VM 設置 AMD GPU 驅動程式
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78269426"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>在運行 Windows 的 N 系列 VM 上安裝 AMD GPU 驅動程式

要利用運行 Windows 的新 Azure NVv4 系列 VM 的 GPU 功能，必須安裝 AMD GPU 驅動程式。 AMD 驅動程式擴展將在未來幾周內提供。 本文提供了支援的作業系統、驅動程式以及手動安裝和驗證步驟。

如需基本規格、儲存體容量與磁碟的詳細資料，請參閱 [GPU Windows VM 大小](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。



## <a name="supported-operating-systems-and-drivers"></a>支援的作業系統和驅動程式

| OS | 驅動程式 |
| -------- |------------- |
| Windows 10 EVD - 生成 1903 <br/><br/>Windows 10 - 生成 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20.Q1.1](https://download.microsoft.com/download/3/8/9/3893407b-e8aa-4079-8592-735d7dd1c19a/Radeon-Pro-Software-for-Enterprise-GA.exe) （.exe） |


## <a name="driver-installation"></a>驅動程式安裝

1. 通過遠端桌面連線到每個 NVv4 系列 VM。

2. 如果您是 NVv4 預覽客戶，請停止 VM 並等待它移動到"已停止（已轉移）"狀態。

3. 請啟動 VM，然後通過運行位於資料夾"._AMDClean 卸載實用程式"的"amdcleanup 實用程式-x64.exe"來卸載預覽驅動程式。 確切的路徑將因以前的驅動程式安裝檔的位置而異。  

4. 下載並安裝最新的驅動程式。

5. 重新啟動 VM。

## <a name="verify-driver-installation"></a>確認驅動程式安裝

您可以在 [裝置管理員] 中確認驅動程式安裝。 下面的示例顯示了 Azure NVv4 VM 上 Radeon 本能 MI25 卡的成功配置。
<br />
![GPU 驅動程式屬性](./media/n-series-amd-driver-setup/device-manager.png)

您可以使用 dxdiag 驗證 GPU 顯示內容，包括視頻 RAM。 下面的示例顯示了 Azure NVv4 VM 上的 Radeon 本能 MI25 卡的第 1/8 個分區。
<br />
![GPU 驅動程式屬性](./media/n-series-amd-driver-setup/dxdiag.png)

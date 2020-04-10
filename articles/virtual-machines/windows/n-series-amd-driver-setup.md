---
title: 適用於 Windows 的 Azure N 系列 AMD GPU 驅動程式設定
description: 如何於 Azure 中執行 Windows 伺服器或 Windows 的 N 系列 VM 設定 AMD GPU 驅動程式
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
ms.openlocfilehash: 02213feb507e9a032a50241fddf31714b9dfd7ee
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011054"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>在執行 Windows 的 N 系列 VM 安裝 AMD GPU 驅動程式

要利用運行 Windows 的新 Azure NVv4 系列 VM 的 GPU 功能,必須安裝 AMD GPU 驅動程式。 AMD 驅動程式擴展將在未來幾周內提供。 本文提供了支援的操作系統、驅動程式以及手動安裝和驗證步驟。

如需基本規格、儲存體容量與磁碟的詳細資料，請參閱 [GPU Windows VM 大小](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。



## <a name="supported-operating-systems-and-drivers"></a>支援的作業系統和驅動程式

| OS | 驅動程式 |
| -------- |------------- |
| Windows 10 EVD - 產生 1903 <br/><br/>Windows 10 - 產生 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20.Q1.1](https://download.microsoft.com/download/3/8/9/3893407b-e8aa-4079-8592-735d7dd1c19a/Radeon-Pro-Software-for-Enterprise-GA.exe) (.exe) |


## <a name="driver-installation"></a>驅動程式安裝

1. 通過遠端桌面連接到每個 NVv4 系列 VM。

2. 如果您是 NVv4 預覽客戶,請停止 VM 並等待它移動到"已停止(已轉移)"狀態。

3. 請啟動 VM 並下載最新的[AMD 清理實用程式](https://download.microsoft.com/download/4/f/1/4f19b714-9304-410f-9c64-826404e07857/AMDCleanupUtilityni.exe)。 通過運行「amdcleanup 實用程式-x64.exe」卸載現有驅動程式。 請不要使用與前一個驅動程式一起安裝的任何現有清理實用程式。  

4. 下載並安裝最新的驅動程式。

5. 重新啟動 VM。

## <a name="verify-driver-installation"></a>確認驅動程式安裝

您可以在 [裝置管理員] 中確認驅動程式安裝。 下面的範例顯示了 Azure NVv4 VM 上 Radeon 本能 MI25 卡的成功配置。
<br />
![GPU 驅動程式屬性](./media/n-series-amd-driver-setup/device-manager.png)

您可以使用 dxdiag 驗證 GPU 顯示屬性,包括視訊 RAM。 下面的範例顯示了 Azure NVv4 VM 上的 Radeon 本能 MI25 卡的 1/2 分區。
<br />
![GPU 驅動程式屬性](./media/n-series-amd-driver-setup/dxdiag-output.png)

如果您正在運行 Windows 10 版本 1903 或更高版本,則 dxdiag 將不會在「顯示」選項卡中顯示任何資訊。請使用底部的「保存所有資訊」選項,輸出檔將顯示與 AMD MI25 GPU 相關的資訊。

![GPU 驅動程式屬性](./media/n-series-amd-driver-setup/dxdiag-details.png)



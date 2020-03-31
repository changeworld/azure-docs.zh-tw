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
ms.openlocfilehash: 883dbc95ee77d03aee4c3231c6ab8c03f9f7f6e4
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387830"
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

3. 請啟動 VM 並下載最新的[AMD 清理實用程式](https://download.microsoft.com/download/4/f/1/4f19b714-9304-410f-9c64-826404e07857/AMDCleanupUtilityni.exe)。 通過運行"amdcleanup 實用程式-x64.exe"卸載現有驅動程式。 請不要使用與前一個驅動程式一起安裝的任何 exititng 清理實用程式。  

4. 下載並安裝最新的驅動程式。

5. 重新啟動 VM。

## <a name="verify-driver-installation"></a>確認驅動程式安裝

您可以在 [裝置管理員] 中確認驅動程式安裝。 下面的示例顯示了 Azure NVv4 VM 上 Radeon 本能 MI25 卡的成功配置。
<br />
![GPU 驅動程式屬性](./media/n-series-amd-driver-setup/device-manager.png)

您可以使用 dxdiag 驗證 GPU 顯示內容，包括視頻 RAM。 下面的示例顯示了 Azure NVv4 VM 上的 Radeon 本能 MI25 卡的第 1/8 個分區。
<br />
![GPU 驅動程式屬性](./media/n-series-amd-driver-setup/dxdiag.png)

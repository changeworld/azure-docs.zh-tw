---
title: 適用於 Windows 的 Azure N 系列 AMD GPU 驅動程式設定
description: 如何針對 Azure 中執行 Windows Server 或 Windows 的 N 系列虛擬機器設定 AMD GPU 驅動程式
author: vikancha-MSFT
manager: jkabat
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: ebae7359c7bd593cb2b63bbd69919330c6aa24f9
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150757"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>在執行 Windows 的 N 系列 VM 上安裝 AMD GPU 驅動程式

若要利用新 Azure NVv4 系列 VM (執行 Windows) 的 GPU 功能，您必須安裝 AMD GPU 驅動程式。 [AMD GPU 驅動程式擴充功能](../extensions/hpccompute-amd-gpu-windows.md)會在 NVv4 系列 VM 上安裝 AMD GPU 驅動程式。 使用 Azure 入口網站或者 Azure PowerShell 或 Azure Resource Manager 範本之類的工具，安裝或管理擴充功能。 如需支援的作業系統和部署步驟，請參閱 [AMD GPU 驅動程式擴充功能文件](../extensions/hpccompute-amd-gpu-windows.md)。

如果您選擇手動安裝 AMD GPU 驅動程式，本文提供支援的作業系統、驅動程式，以及安裝和驗證步驟。

NVv4 VM 只支援 Microsoft 所發佈的 GPU 驅動程式。 請「不要」從任何其他來源安裝 GPU 驅動程式。

如需基本規格、儲存體容量與磁碟的詳細資料，請參閱 [GPU Windows VM 大小](../sizes-gpu.md?toc=/azure/virtual-machines/windows/toc.json)。



## <a name="supported-operating-systems-and-drivers"></a>支援的作業系統和驅動程式

| OS | 驅動程式 |
| -------- |------------- |
| Windows 10 企業版多會話-組建1903 <br/><br/>Windows 10 - 組建 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20. Q1。](https://download.microsoft.com/download/d/e/f/def0fb44-15ab-4b83-959a-8094eb9d0dfe/AMD-Azure-NVv4-Driver-20Q1-Hotfix3.exe) ( .exe) 的修正程式 |


## <a name="driver-installation"></a>驅動程式安裝

1. 由遠端桌面連線至每個 NVv4 系列 VM。

2. 如果您需要卸載先前的驅動程式版本，請在 [這裡](https://download.microsoft.com/download/4/f/1/4f19b714-9304-410f-9c64-826404e07857/AMDCleanupUtilityni.exe) 下載 AMD 清理公用程式，請勿使用舊版驅動程式隨附的公用程式。

3. 下載並安裝最新版驅動程式。

4. 重新啟動 VM。

## <a name="verify-driver-installation"></a>確認驅動程式安裝

您可以在 [裝置管理員] 中確認驅動程式安裝。 下列範例會顯示 Azure NVv4 VM 上成功的 Radeon Instinct MI25 卡組態。
<br />
![GPU 裝置狀態](./media/n-series-amd-driver-setup/device-manager.png)

您可以使用 dxdiag 來驗證 GPU 顯示內容，包括影片 RAM。 下列範例會顯示 Azure NVv4 VM 上 Radeon Instinct MI25 卡的 1/2 分割區。
<br />
![GPU 屬性](./media/n-series-amd-driver-setup/dxdiag-output-new.png)

如果您執行的是 Windows 10 組建 1903 或更新版本，則 dxdiag 不會在 [顯示] 索引標籤中顯示任何資訊。請使用底部的 [儲存所有資訊] 選項，輸出檔案將會顯示與 AMD MI25 GPU 相關的資訊。

![GPU 驅動程式屬性](./media/n-series-amd-driver-setup/dxdiag-details.png)

---
title: Azure 中的 VM 開機診斷 | Microsoft Docs
description: Azure 中的虛擬機器兩個偵錯功能的概觀
services: virtual-machines
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: delhan
ms.openlocfilehash: 236390529ddaf84416448138d7b5bdf31caf681b
ms.sourcegitcommit: 42922af070f7edf3639a79b1a60565d90bb801c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2020
ms.locfileid: "97827490"
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-virtual-machines-in-azure"></a>如何使用開機診斷對 Azure 中的虛擬機器進行疑難排解

有許多原因會讓虛擬機器進入無法開機的狀態。 若要解決使用 Resource Manager 部署模型建立之虛擬機器的問題，您可以使用下列偵錯工具功能：適用于 Azure 虛擬機器的主控台輸出和螢幕擷取畫面支援。 

若為 Linux 虛擬機器，您可以在入口網站檢視主控台記錄的輸出。 若為 Windows 和 Linux 虛擬機器，Azure 可讓您從 Hypervisor 查看 VM 的螢幕擷取畫面。 所有區域中的 Azure 虛擬機器都支援這兩項功能。 注意：螢幕擷取畫面和輸出最多可能需要10分鐘的時間才會出現在您的儲存體帳戶中。

您可以選取 [開機診斷] 選項來檢視記錄和螢幕擷取畫面。

![Resource Manager](./media/virtual-machines-common-boot-diagnostics/screenshot1.png)

## <a name="common-boot-errors"></a>常見的開機錯誤

- [0xC000000E](https://support.microsoft.com/help/4010129)
- [0xC000000F](https://support.microsoft.com/help/4010130)
- [0xC0000011](https://support.microsoft.com/help/4010134)
- [0xC0000034](https://support.microsoft.com/help/4010140)
- [0xC0000098](https://support.microsoft.com/help/4010137)
- [0xC00000BA](https://support.microsoft.com/help/4010136)
- [0xC000014C](https://support.microsoft.com/help/4010141)
- [0xC0000221](https://support.microsoft.com/help/4010132)
- [0xC0000225](https://support.microsoft.com/help/4010138)
- [0xC0000359](https://support.microsoft.com/help/4010135)
- [0xC0000605](https://support.microsoft.com/help/4010131)
- [找不到作業系統](https://support.microsoft.com/help/4010142)
- [開機失敗或 INACCESSIBLE_BOOT_DEVICE](https://support.microsoft.com/help/4010143)

## <a name="enable-diagnostics-on-a-virtual-machine-created-using-the-azure-portal"></a>在使用 Azure 入口網站所建立的虛擬機器上啟用診斷

下列程序適用於使用 Resource Manager 部署模型所建立的虛擬機器。

在 [管理] 索引標籤上的 [監視] 區段中，確定 [開機診斷] 已開啟。 從 [診斷儲存體帳戶] 下拉式清單中，選取要用來放置診斷檔案的儲存體帳戶。
 
![建立 VM](./media/virtual-machines-common-boot-diagnostics/enable-boot-diagnostics-vm.png)

> [!NOTE]
> 開機診斷功能不支援 premium 儲存體帳戶或區域多餘的儲存體帳戶類型。 如果您使用進階儲存體帳戶來進行開機診斷，可能會在啟動虛擬機器時收到 StorageAccountTypeNotSupported 錯誤。
>

### <a name="deploying-from-an-azure-resource-manager-template"></a>從 Azure Resource Manager 範本部署

如果您正以 Azure Resource Manager 範本進行部署，請巡覽至您的虛擬機器的資源並附加診斷設定檔區段。 將 API 版本標頭設定為 "2015-06-15" 或更新版本。 最新版本是 "2018-10-01"。

```json
{
  "apiVersion": "2018-10-01",
  "type": "Microsoft.Compute/virtualMachines",
  … 
```

診斷設定檔可讓您選取想要放置這些記錄的儲存體帳戶。

```json
    "diagnosticsProfile": {
    "bootDiagnostics": {
    "enabled": true,
    "storageUri": "[concat('https://', parameters('newStorageAccountName'), '.blob.core.windows.net')]"
    }
    }
    }
}
```

如需使用範本部署資源的詳細資訊，請參閱 [快速入門：使用 Azure 入口網站來建立和部署 Azure Resource Manager 範本](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)。

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>在現有的虛擬機器上啟用開機診斷 

若要在現有的虛擬機器上啟用開機診斷功能，請遵循下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後選取虛擬機器。
2. 在 [支援 + 疑難排解] 區段中，選取 [開機診斷]，然後選取 [設定] 索引標籤。
3. 在 [開機診斷] 設定中，將狀態變更為 [開啟]，然後從 [儲存體帳戶] 下拉式清單選取儲存體帳戶。 
4. 儲存變更。

    ![更新現有的 VM](./media/virtual-machines-common-boot-diagnostics/enable-for-existing-vm.png)

### <a name="enable-boot-diagnostics-using-the-azure-cli"></a>使用 Azure CLI 啟用開機診斷

您可以使用 Azure CLI 在現有的 Azure 虛擬機器上啟用開機診斷。 如需詳細資訊，請參閱 [az vm boot-diagnostics](/cli/azure/vm/boot-diagnostics?view=azure-cli-latest)。

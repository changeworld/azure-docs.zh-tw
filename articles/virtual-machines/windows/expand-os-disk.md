---
title: 在 Azure 中擴充 Windows VM 的 OS 磁碟機
description: 使用 Resource Manager 部署模型中的 Azure PowerShell 展開虛擬機器的 OS 磁片磁碟機大小。
services: virtual-machines-windows
documentationcenter: ''
author: kirpasingh
manager: roshar
editor: ''
tags: azure-resource-manager
ms.assetid: d9edfd9f-482f-4c0b-956c-0d2c2c30026c
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/02/2020
ms.author: kirpas
ms.subservice: disks
ms.openlocfilehash: 3f1c7fb08c3dcaa078de8f1ee0b90409289cfb43
ms.sourcegitcommit: 4feb198becb7a6ff9e6b42be9185e07539022f17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2020
ms.locfileid: "89469207"
---
# <a name="how-to-expand-the-os-drive-of-a-virtual-machine"></a>如何擴充虛擬機器的 OS 磁碟機

當您從 [Azure Marketplace](https://azure.microsoft.com/marketplace/)部署映射以在資源群組中建立新的虛擬機器 (VM) 時，預設的 os 磁片磁碟機通常是 127 GB (有些映射預設會有較小的 OS 磁片大小) 。 雖然您可以將資料磁片新增至 VM (數目取決於您選擇) 的 SKU，建議您在這些增補磁片上安裝應用程式和 CPU 密集的工作負載，而客戶通常需要擴充作業系統磁片磁碟機以支援特定案例：

- 支援在作業系統磁片磁碟機上安裝元件的繼承應用程式。
- 使用較大的 OS 磁片磁碟機從內部部署遷移實體電腦或 VM。

> [!IMPORTANT]
> 必須將虛擬機器解除配置，才能調整 Azure 虛擬機器的 OS 磁碟大小。
>
> 在擴充磁碟之後，您必須[擴充 OS 內的磁碟區](#expand-the-volume-within-the-os)以使用較大的磁碟。
> 

## <a name="resize-a-managed-disk-in-the-azure-portal"></a>調整 Azure 入口網站中的受控磁片大小

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，移至您要在其中擴充磁片的虛擬機器。 選取 [ **停止** ] 以將 VM 解除配置。
2. 當 VM 停止時，請在左側功能表的 [ **設定**] 底下，選取 [ **磁片**]。

    :::image type="content" source="./media/expand-os-disk/select-disks.png" alt-text="顯示在功能表的 [設定] 區段中選取 [磁片] 選項的螢幕擷取畫面。":::

 
3. 在 [ **磁片名稱**] 下，選取您要調整大小的磁片。

    :::image type="content" source="./media/expand-os-disk/disk-name.png" alt-text="螢幕擷取畫面，顯示已選取磁片名稱的 [磁片] 窗格。":::

4. 在左側功能表的 [ **設定**] 底下 **，選取 [** 設定]。

    :::image type="content" source="./media/expand-os-disk/configuration.png" alt-text="顯示在功能表的 [設定] 區段中選取之設定選項的螢幕擷取畫面。":::

5. 在 [ **大小] (GiB]) **中，選取您要的磁片大小。
   
   > [!WARNING]
   > 新的大小應該大於現有的磁碟大小。 針對 OS 磁片，允許的最大值為 2048 GB。  (可以將 VHD blob 擴充至超過該大小，但 OS 只能與前 2048 GB 的空間搭配使用。 ) 
   > 

    :::image type="content" source="./media/expand-os-disk/size.png" alt-text="螢幕擷取畫面，顯示已選取磁片大小的設定窗格。":::

6. 選取 [儲存]。

    :::image type="content" source="./media/expand-os-disk/save.png" alt-text="顯示 [設定] 窗格的螢幕擷取畫面，其中已選取 [儲存] 按鈕。":::


## <a name="resize-a-managed-disk-by-using-powershell"></a>使用 PowerShell 調整受控磁片大小

以系統管理模式開啟您的 PowerShell ISE 或 PowerShell 視窗，並遵循下列步驟：

1. 在資源管理模式下登入您的 Microsoft Azure 帳戶，然後選取您的訂用帳戶：
   
    ```powershell
    Connect-AzAccount
    Select-AzSubscription –SubscriptionName 'my-subscription-name'
    ```

2. 設定您的資源組名和 VM 名稱：
   
    ```powershell
    $rgName = 'my-resource-group-name'
    $vmName = 'my-vm-name'
    ```

3. 取得 VM 的參考：
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

4. 調整磁片大小之前，請先停止 VM：
   
    ```powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

5. 取得受控 OS 磁碟的參考。 將受控作業系統磁片的大小設定為所需的值並更新磁片：
   
    ```powershell
    $disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
    $disk.DiskSizeGB = 1023
    Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
    ```   
    > [!WARNING]
    > 新的大小應該大於現有的磁碟大小。 針對 OS 磁片，允許的最大值為 2048 GB。  (可以將 VHD blob 擴充至超過該大小，但 OS 只能與前 2048 GB 的空間搭配使用。 ) 
    > 
         
6. 更新 VM 可能需要幾秒鐘的時間。 當命令執行完成時，請重新開機 VM：
   
    ```powershell
    Start-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

這樣就大功告成了！ 現在 RDP 到 VM，開啟 [電腦管理] \(或 [磁碟管理])，然後使用剛配置的空間擴充磁碟機。

## <a name="resize-an-unmanaged-disk-by-using-powershell"></a>使用 PowerShell 調整非受控磁片大小

以系統管理模式開啟您的 PowerShell ISE 或 PowerShell 視窗，並遵循下列步驟：

1. 在資源管理模式下登入您的 Microsoft Azure 帳戶，然後選取您的訂用帳戶：
   
    ```powershell
    Connect-AzAccount
    Select-AzSubscription –SubscriptionName 'my-subscription-name'
    ```

2. 設定您的資源組名和 VM 名稱：
   
    ```powershell
    $rgName = 'my-resource-group-name'
    $vmName = 'my-vm-name'
    ```

3. 取得 VM 的參考：
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

4. 調整磁片大小之前，請先停止 VM：
   
    ```powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

5. 將非受控 OS 磁片的大小設定為所需的值並更新 VM：
   
    ```powershell
    $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
    Update-AzVM -ResourceGroupName $rgName -VM $vm
    ```
   
    > [!WARNING]
    > 新的大小應該大於現有的磁碟大小。 針對 OS 磁片，允許的最大值為 2048 GB。  (可以將 VHD blob 擴充到該大小之後，但 OS 只能使用前 2048 GB 的空間。 ) 
    > 
    > 
   
6. 更新 VM 可能需要幾秒鐘的時間。 當命令執行完成時，請重新開機 VM：
   
    ```powershell
    Start-AzVM -ResourceGroupName $rgName -Name $vmName
    ```


## <a name="scripts-for-os-disk"></a>用於 OS 磁碟的指令碼

以下為受控和非受控磁碟的完整指令碼，供您參考：


**受控磁碟**

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
Stop-AzVM -ResourceGroupName $rgName -Name $vmName
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
$disk.DiskSizeGB = 1023
Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

**非受控磁碟**

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
Stop-AzVM -ResourceGroupName $rgName -Name $vmName
$vm.StorageProfile.OSDisk.DiskSizeGB = 1023
Update-AzVM -ResourceGroupName $rgName -VM $vm
Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="resizing-data-disks"></a>調整資料磁碟大小

本文主要著重於擴充 VM 的 OS 磁碟，但指令碼也可用於擴充連結到 VM 的資料磁碟。 例如，若要擴充連接至 VM 的第一個資料磁碟，請將 `StorageProfile` 的 `OSDisk` 物件取代成 `DataDisks` 陣列，並使用數值索引取得第一個連接的資料磁碟的參考，如下所示︰

**受控磁碟**

```powershell
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```

**非受控磁碟**

```powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```

同樣地，您可以使用如上所示的索引或磁片的 [ **名稱** ] 屬性，參考連接至 VM 的其他資料磁片：


**受控磁碟**

```powershell
(Get-AzDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

**非受控磁碟**

```powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'}).DiskSizeGB = 1023
```

## <a name="expand-the-volume-within-the-os"></a>擴充 OS 內的磁碟區

當您擴充 VM 的磁片時，您必須移至 OS，並展開磁片區以包含新的空間。 有數種方法可以擴充磁碟分割。 本章節說明如何使用 RDP 連線與 VM 建立連線，以使用 **DiskPart**擴充磁碟分割。

1. 開啟連到 VM 的 RDP 連線。

2. 開啟命令提示字元，然後鍵入 **diskpart**。

3. 在 **DISKPART** 提示處，鍵入 `list volume`。 記下您想要擴充的磁碟區。

4. 在 **DISKPART** 提示處，鍵入 `select volume <volumenumber>`。 如此會選取您想在相同磁碟上擴充成連續空間的磁碟區 *volumenumber*。

5. 在 **DISKPART** 提示處，鍵入 `extend [size=<size>]`。 如此會以 *size* (MB) 為單位，擴充選取的磁碟區。


## <a name="next-steps"></a>後續步驟

您也可使用 [Azure 入口網站](attach-managed-disk-portal.md)連結磁碟。

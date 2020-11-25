---
title: 包含檔案
description: 檔案
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: mingshen-ms
ms.author: krsh
ms.date: 10/20/2020
ms.openlocfilehash: 6c7536e38a0d2cf7d4e906947aff645c74e459c0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96028576"
---
## <a name="generalize-the-image"></a>一般化映像

Azure Marketplace 中的所有映像通常都必須能夠重複使用。 若要達到此目標，則必須將作業系統 VHD 一般化，此作業會移除 VM 中所有的執行個體特定識別碼和軟體驅動程式。

### <a name="for-windows"></a>若為 Windows

Windows 作業系統磁片會以 [sysprep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) 工具一般化。 如果您稍後更新或重新設定 OS，則必須再次執行 sysprep。

> [!WARNING]
> 執行 sysprep 之後，請將 VM 關閉直到部署完成，因為更新可能會自動執行。 關閉動作可避免後續更新對作業系統或已安裝的服務進行執行個體特定變更。 如需執行 sysprep 的詳細資訊，請參閱[將 VHD 一般化的步驟](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)。

### <a name="for-linux"></a>若為 Linux

下列程序會將 Linux VM 一般化，並重新部署為個別的 VM。 如需詳細資料，請參閱[如何建立虛擬機器或 VHD 的映像](../../virtual-machines/linux/capture-image.md) (機器翻譯)。 當您到達稱為「從已捕獲映射建立 VM」的區段時，您可以停止。

1. 移除 Azure Linux 代理程式。
    1. 使用 SSH 用戶端連線到 Linux VM。
    2. 在 SSH 視窗中，輸入下列命令： `sudo waagent –deprovision+user` 。
    3. 鍵入 Y 繼續進行 (可新增 -force 參數至上一個命令，即無須執行此確認步驟)。
    4. 命令完成之後，請輸入 **Exit** 以關閉 SSH 用戶端。
2. 停止虛擬機器。
    1. 在 Azure 入口網站，選取資源群組 (RG) 並解除配置 VM。
    2. 現在您的 VM 已一般化，且您可以使用此 VM 磁片來建立新的 VM。

### <a name="take-a-snapshot-of-the-vm-disk"></a>製作 VM 磁片的快照集

1. 登入 [Azure 入口網站](https://ms.portal.azure.com/)。
2. 從左上方開始，選取 [ **建立資源**]，然後搜尋並選取 [ **快照** 集]。
3. 在 [快照集] 分頁中，選取 [  **建立**]。
4. 輸入快照集的 [名稱]。
5. 選取現有的資源群組，或輸入新的資源群組的名稱。
6. 在 [來源磁碟] 中，選取要建立快照集的受控磁碟。
7. 選取用來儲存快照集的 [帳戶類型]。 除非需要存放在高效能的 SSD 上，否則請使用 [標準 HDD]。
8. 選取 [建立]。

#### <a name="extract-the-vhd"></a>將 VHD 解壓縮

使用下列腳本，將快照集匯出至儲存體帳戶中的 VHD。

```JSON
#Provide the subscription Id where the snapshot is created
subscriptionId=yourSubscriptionId

#Provide the name of your resource group where the snapshot is created
resourceGroupName=myResourceGroupName

#Provide the snapshot name
snapshotName=mySnapshot

#Provide Shared Access Signature (SAS) expiry duration in seconds (such as 3600)
#Know more about SAS here: https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-shared-access-signature-part-1
sasExpiryDuration=3600

#Provide storage account name where you want to copy the underlying VHD file. 
storageAccountName=mystorageaccountname

#Name of the storage container where the downloaded VHD will be stored.
storageContainerName=mystoragecontainername

#Provide the key of the storage account where you want to copy the VHD 
storageAccountKey=mystorageaccountkey

#Give a name to the destination VHD file to which the VHD will be copied.
destinationVHDFileName=myvhdfilename.vhd

az account set --subscription $subscriptionId

sas=$(az snapshot grant-access --resource-group $resourceGroupName --name $snapshotName --duration-in-seconds $sasExpiryDuration --query [accessSas] -o tsv)

az storage blob copy start --destination-blob $destinationVHDFileName --destination-container $storageContainerName --account-name $storageAccountName --account-key $storageAccountKey --source-uri $sas
```

#### <a name="script-explanation"></a>指令碼說明

此腳本會使用下列命令來產生快照集的 SAS URI，並使用 SAS URI 將基礎 VHD 複製到儲存體帳戶。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
| --- | --- |
| az disk grant-access | 產生唯讀 SAS，用來將基礎 VHD 檔案複製到儲存體帳戶，或將它下載到內部部署
| az storage blob copy start | 以非同步方式從一個儲存體帳戶將 blob 複製到另一個儲存體帳戶。 用 `az storage blob show` 來檢查新 blob 的狀態。 |
|

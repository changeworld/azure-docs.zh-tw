---
title: 在 Azure Cloud Shell 中保存檔案 |Microsoft Docs
description: 逐步解說 Azure Cloud Shell 如何保存檔案。
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/24/2020
ms.author: damaerte
ms.openlocfilehash: 37005a722d4a1962b4f6e1ddb8bb1c7a1229d28a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81273285"
---
# <a name="persist-files-in-azure-cloud-shell"></a>在 Azure Cloud Shell 中保存檔案
Cloud Shell 會運用 Azure 檔案儲存體在工作階段間保存檔案。 在初始啟動時，Cloud Shell 會提示您關聯新的或現有的檔案共用，以在工作階段間保存檔案。

> [!NOTE]
> Bash 和 PowerShell 會共用相同的檔案共用。 只有一個檔案共用可以與 Cloud Shell 中的自動掛接相關聯。

> [!NOTE]
> Cloud shell 儲存體帳戶不支援 Azure 儲存體防火牆。

## <a name="create-new-storage"></a>建立新的儲存體

當您使用基本設定並只選取訂用帳戶時，Cloud Shell 會代表您在距離您最近的支援區域中建立三個資源：
* 資源群組：`cloud-shell-storage-<region>`
* 儲存體帳戶：`cs<uniqueGuid>`
* 檔案共用：`cs-<user>-<domain>-com-<uniqueGuid>`

![訂用帳戶設定](media/persisting-shell-storage/basic-storage.png)

檔案共用會在您的 `$Home` 目錄中掛接為 `clouddrive`。 這是一次性的動作，後續的工作階段會自動掛接檔案共用。 

檔案共用也包含為您建立的 5 GB 映像，此映像會自動保存 `$Home` 目錄中的資料。 這適用於 Bash 和 PowerShell。

## <a name="use-existing-resources"></a>使用現有的資源

您可以使用進階選項來建立與現有資源的關聯。 選取 Cloud Shell 區域時，您也必須選取在同一個區域中共置的備份儲存體帳戶。 例如，如果您指派的區域是「美國西部」，則您也必須將位於美國西部的檔案共用關聯在一起。

當儲存體設定提示出現時，請選取 [顯示進階設定]**** 以檢視其他選項。 已填入的儲存體選項會篩選本地備援儲存體 (LRS)、異地備援儲存體 (GRS) 和區域備援儲存體 (ZRS) 帳戶。 

> [!NOTE]
> 建議使用 GRS 或 ZRS 儲存體帳戶，如此您的備份檔案共用能獲得更多復原能力。 備援類型取決於您的目標和偏好的價格。 [深入了解 Azure 儲存體帳戶的複寫選項](../storage/common/storage-redundancy.md)。

![資源群組設定](media/persisting-shell-storage/advanced-storage.png)

## <a name="securing-storage-access"></a>保護儲存體存取
為了安全起見，每位使用者都應該佈建自己的儲存體帳戶。  若要進行角色型存取控制 (RBAC)，使用者在儲存體帳戶層級必須具備參與者權限或更高的存取權限。

Cloud Shell 在指定的訂用帳戶內，使用儲存體帳戶中的 Azure 檔案共用。 由於繼承的許可權，擁有訂用帳戶的足夠存取權限的使用者將能夠存取所有儲存體帳戶，以及訂用帳戶中包含的檔案共用。

使用者應該藉由設定儲存體帳戶或訂用帳戶層級的許可權，來鎖定其檔案的存取權。

## <a name="supported-storage-regions"></a>支援的儲存體區域
若要尋找您目前的區域，您可以 `env` 在 Bash 中執行，並找出變數 `ACC_LOCATION` 或從 PowerShell 執行 `$env:ACC_LOCATION` 。 檔案共用會收到為您建立以便保存 `$Home` 目錄的 5 GB 映像。

Cloud Shell 電腦存在於下列區域：

|區域|區域|
|---|---|
|美洲|美國東部、美國中南部、美國西部|
|歐洲|北歐、西歐|
|亞太地區|印度中部、東南亞|

客戶應選擇主要區域，除非他們的待用資料必須儲存在特定區域中。 如果有這類需求，就應該使用次要儲存體區域。

### <a name="secondary-storage-regions"></a>次要儲存體區域
如果使用次要儲存體區域，相關聯的 Azure 儲存體帳戶會位於與您要掛接的 Cloud Shell 機不同的區域中。 例如，Jane 可以將她的儲存體帳戶設定為位於加拿大東部（次要地區），但她掛接到的機器仍然位於主要區域中。 她的待用資料位於加拿大，但在美國進行處理。

> [!NOTE]
> 如果使用次要區域，Cloud Shell 的檔案存取和啟動時間可能會較慢。

使用者可以 `(Get-CloudDrive | Get-AzStorageAccount).Location` 在 PowerShell 中執行，以查看其檔案共用的位置。

## <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>使用 Azure 資源原則限制資源建立
您在 Cloud Shell 中建立的儲存體帳戶都會標記 `ms-resource-usage:azure-cloud-shell`。 如果您想要禁止使用者在 Cloud Shell 中建立儲存體帳戶，請建立這個特定標籤所觸發之[標籤的 Azure 資源原則](../azure-policy/json-samples.md)。

## <a name="how-cloud-shell-storage-works"></a>Cloud Shell 儲存體的運作方式 
Cloud Shell 透過下列兩種方法來保存檔案： 
* 建立 `$Home` 目錄的磁碟映像，以保存該目錄內的所有內容。 此磁碟映像會在您指定的檔案共用中儲存為 `acc_<User>.img` (位於 `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img`)，並自動同步變更。 
* 在您的 `$Home` 目錄中，將指定的檔案共用掛接為 `clouddrive`，以便直接與檔案共用互動。 `/Home/<User>/clouddrive` 對應至 `fileshare.storage.windows.net/fileshare`。
 
> [!NOTE]
> `$Home` 目錄中的所有檔案 (例如 SSH 金鑰) 會都保存於已掛接檔案共用中儲存的使用者磁碟映像中。 當您在 `$Home` 目錄和已掛接的檔案共用中保存資訊時，請套用最佳做法。

## <a name="clouddrive-commands"></a>clouddrive 命令

### <a name="use-the-clouddrive-command"></a>使用 `clouddrive` 命令
在 Cloud Shell 中，您可以執行名為的命令 `clouddrive` ，讓您手動更新已掛接至 Cloud Shell 的檔案共用。

![執行 "clouddrive" 命令](media/persisting-shell-storage/clouddrive-h.png)

### <a name="list-clouddrive"></a>列出 `clouddrive`
若要探索掛接為 `clouddrive` 的檔案共用，請執行 `df` 命令。 

clouddrive 檔案路徑會在 URL 中顯示您的儲存體帳戶名稱和檔案共用。 例如， `//storageaccountname.file.core.windows.net/filesharename`

```
justin@Azure:~$ df
Filesystem                                          1K-blocks   Used  Available Use% Mounted on
overlay                                             29711408 5577940   24117084  19% /
tmpfs                                                 986716       0     986716   0% /dev
tmpfs                                                 986716       0     986716   0% /sys/fs/cgroup
/dev/sda1                                           29711408 5577940   24117084  19% /etc/hosts
shm                                                    65536       0      65536   0% /dev/shm
//mystoragename.file.core.windows.net/fileshareName 5368709120    64 5368709056   1% /home/justin/clouddrive
justin@Azure:~$
```

### <a name="mount-a-new-clouddrive"></a>掛接新的 clouddrive

#### <a name="prerequisites-for-manual-mounting"></a>手動掛接的先決條件
您可以使用 `clouddrive mount` 命令來更新與 Cloud Shell 關聯的檔案共用。

如果您要掛接現有的檔案共用，儲存體帳戶必須位在所選的 Cloud Shell 區域中： 執行並檢查，以取出位置 `env` `ACC_LOCATION` 。

#### <a name="the-clouddrive-mount-command"></a>`clouddrive mount` 命令

> [!NOTE]
> 如果您要掛接新的檔案共用，則系統會建立您 `$Home` 目錄的新使用者映像。 您的先前 `$Home` 映像會保留在先前的檔案共用中。

執行 `clouddrive mount` 命令搭配下列參數：

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

若要檢視更多詳細資料，請執行 `clouddrive mount -h`，如下所示：

![執行 `clouddrive mount` 命令](media/persisting-shell-storage/mount-h.png)

### <a name="unmount-clouddrive"></a>卸載 clouddrive
您可以隨時將掛接至 Cloud Shell 的檔案共用卸載。 因為 Cloud Shell 需要使用掛接的檔案共用，所以系統會在下一個工作階段提示您建立和掛接另一個檔案共用。

1. 執行 `clouddrive unmount`。
2. 了解並確認提示。

除非手動刪除，否則您的檔案共用將會繼續存在。 Cloud Shell 在後續的工作階段中將不再搜尋此檔案共用。 若要檢視更多詳細資料，請執行 `clouddrive unmount -h`，如下所示：

![執行 `clouddrive unmount` 命令](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> 雖然執行此命令不會刪除任何資源，但手動刪除對應至 Cloud Shell 的資源群組、儲存體帳戶或檔案共用，將會清除您的 `$Home` 目錄磁碟映像及檔案共用中的任何檔案。 此動作無法復原。
## <a name="powershell-specific-commands"></a>PowerShell 特有的命令

### <a name="list-clouddrive-azure-file-shares"></a>列出 `clouddrive` Azure 檔案共用
`Get-CloudDrive` Cmdlet 會擷取 Cloud Shell 中 `clouddrive` 目前所掛接的 Azure 檔案共用資訊。 <br>
![Running Get-CloudDrive](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

### <a name="unmount-clouddrive"></a>卸載 `clouddrive`
您可以隨時將掛接至 Cloud Shell 的 Azure 檔案共用卸載。 如果已移除 Azure 檔案共用，則系統會在下一個工作階段提示您建立和掛接新的 Azure 檔案共用。

`Dismount-CloudDrive` Cmdlet 會從目前儲存體帳戶卸載 Azure 檔案共用。 卸載 `clouddrive` 會終止目前工作階段。 系統會提示使用者在下一個工作階段期間建立和掛接新的 Azure 檔案共用。
![執行 Dismount-CloudDrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [PersistingStorage-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

注意：如果您需要在檔案中定義函式，並從 PowerShell Cmdlet 呼叫它，則必須包含點運算子。 例如： .\MyFunctions.ps1

## <a name="next-steps"></a>後續步驟
[Cloud Shell 快速入門](quickstart.md) <br>
[了解 Microsoft Azure 檔案儲存體](../storage/files/storage-files-introduction.md) <br>
[深入瞭解儲存體標記](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>

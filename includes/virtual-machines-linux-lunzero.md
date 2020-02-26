---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 09fa612e7e5c681da16bf19e94c626ae14a3b8a1
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77590835"
---
將資料磁碟新增到 Linux VM 時，如果 LUN 0 沒有磁碟，可能會發生錯誤。 如果您是藉由使用 `az vm disk attach -new` 命令並指定 LUN (`--lun`) 來手動新增磁碟，而不是讓 Azure 平台判斷適當的 LUN，則請注意，LUN 0 已經有磁碟或將會有磁碟。 

請思考一下以下範例，此範例顯示來自 `lsscsi`之輸出的程式碼片段：

```bash
[5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc 
[5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd 
```

兩個資料磁碟存在於 LUN 0 和 LUN 1 (`lsscsi` 輸出中的第 1 欄輸出了詳細資料 `[host:channel:target:lun]`)。 兩個磁碟應該都要是可從 VM 內存取的磁碟。 如果您已手動指定要在 LUN 1 新增第一個磁碟及在 LUN 2 新增第二個磁碟，則從您的 VM 內可能無法正確看見這些磁碟。

> [!NOTE]
> 在這些範例中，Azure `host` 值為 5，但這可能會依據您選取的儲存體類型而有所不同。
> 
> 

此磁碟行為不是 Azure 問題，而是 Linux 核心遵循 SCSI 規格的方式。 當 Linux 核心掃描 SCSI 匯流排是否有已連接的裝置時，必須在 LUN 0 找到裝置，系統才能繼續掃描是否有其他裝置。 如以上所述，因此︰

* 在新增資料磁碟之後，請檢閱 `lsscsi` 的輸出，以確認 LUN 0 有磁碟。
* 如果磁碟在 VM 內未正確顯示，請確認 LUN 0 有磁碟。


---
title: Linux 虛擬機器上完整 OS 磁片的問題
description: 如何解決 Linux 虛擬機器上完整 OS 磁片的問題
author: v-miegge
ms.author: tibasham
manager: dcscontentpm
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 11/20/2020
ms.openlocfilehash: 2e350fe297b2aaf89ac302baaefb29092cfe2532
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523412"
---
# <a name="issues-with-a-full-os-disk-on-a-linux-virtual-machine"></a>Linux 虛擬機器上完整 OS 磁片的問題

當 Linux 虛擬機器的 OS 磁片 (VM) 已滿時，這可能會導致 VM 的適當操作發生問題。

## <a name="symptom"></a>徵狀

當您嘗試建立新檔案時，您會收到下列訊息：

```
username@AZUbuntu1404:~$ touch new 
touch: cannot touch “new”: No space left on device 
username@AZUbuntu1404:~$
```

然後，多個守護程式表示它們無法在開機會話期間建立暫存檔案。

```
ERROR:IOError: [Errno 28] No space left on device: '/var/lib/waagent/events/1474306860983232.tmp' 
    
OSError: [Errno 28] No space left on device: '/var/lib/cloud/data/tmpDZCq0g'
```
    
## <a name="cause"></a>原因

有幾個原因會造成此錯誤訊息：

1. 磁片可能已滿。
1. 檔案系統的 Inode 可能已用盡。
1. 可能會將資料磁片掛接在現有的目錄上，導致隱藏盤案。
1. 由進程開啟然後刪除的檔案。

## <a name="solution"></a>解決方法

### <a name="process-overview"></a>程序概觀

1. 建立和存取修復 VM。
1. 磁碟上的可用空間。
1. 重建 VM。

> [!NOTE]
> 發生此錯誤時，客體作業系統將無法運作。 請在離線模式中針對此問題進行疑難排解，以解決此問題。

### <a name="create-and-access-a-repair-vm"></a>建立及存取修復 VM

1. 使用 [VM 修復命令](./repair-linux-vm-using-azure-virtual-machine-repair-commands.md) \(部分機器翻譯\) 的步驟 1-3 準備修復 VM。
1. 使用 SSH 連接到修復 VM。

### <a name="free-up-space-on-the-disk"></a>釋放磁片上的空間

若要解決此問題：

- 如果磁碟的大小上限不是 1 TB，請將其大小調整為 1 TB。
- 釋放磁碟空間。

1. 檢查磁碟是否已滿。 如果磁片大小低於 1 TB，請 [使用 Azure CLI](../linux/expand-disks.md)最多可將其擴展為 1 tb。
1. 如果磁片已經是 1 TB，您將需要釋放磁碟空間。
1. 調整大小和清除之後，會繼續重建 VM。

### <a name="rebuild-the-vm"></a>重建 VM

使用 [VM 修復命令的步驟 5](./repair-linux-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example)\(部分機器翻譯\)重建 VM。

---
title: 從 Azure 下載 Linux VHD
description: 使用 Azure CLI 和 Azure 入口網站來下載 Linux VHD。
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 08/03/2020
ms.author: cynthn
ms.openlocfilehash: 897cae53e589f4058e5499c0e6e941d4f1d9bb2f
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/04/2020
ms.locfileid: "87761041"
---
# <a name="download-a-linux-vhd-from-azure"></a>從 Azure 下載 Linux VHD

在本文中，您將瞭解如何使用 Azure 入口網站從 Azure 下載 Linux 虛擬硬碟 (VHD) 檔案。 

## <a name="stop-the-vm"></a>停止 VM

如果 VHD 連接至執行中的 VM，便無法從 Azure 下載該 VHD。 您需要停止 VM 以下載 VHD。 

1.  登入 [Azure 入口網站](https://portal.azure.com/)。
2.  在左側功能表上，選取 [**虛擬機器**]。
3.  從清單中選取 VM。
4.  在 VM 的頁面上，選取 [**停止**]。

    :::image type="content" source="./media/download-vhd/export-stop.PNG" alt-text="顯示用來停止 VM 的功能表按鈕。":::

## <a name="generate-sas-url"></a>產生 SAS URL

若要下載 VHD 檔案，您需要[ (SAS) URL 產生共用存取](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json)簽章。 產生 URL 時，會將到期時間指派給 URL。

1. 在 VM 頁面的功能表上，選取 [**磁片**]。
2. 選取 VM 的作業系統磁碟，然後選取 [磁碟匯出]****。
1. 如果需要，請更新 [URL] 的值（**以 (秒為單位）) ** ，讓您有足夠的時間完成下載。 預設值為3600秒 (一小時) 。
3. 選取 [產生 URL]****。
 
      
## <a name="download-vhd"></a>下載 VHD

1.  在產生的 URL 之下，選取 [下載 VHD 檔案]****。

    :::image type="content" source="./media/download-vhd/export-download.PNG" alt-text="顯示用來下載 VHD 的按鈕。":::

2.  您可能需要在瀏覽器中選取 [**儲存**]，才能開始下載。 VHD 檔案的預設名稱是 *abcd*。

## <a name="next-steps"></a>後續步驟

- 了解如何[使用 Azure CLI 從自訂磁碟上傳並建立 Linux VM](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 
- [使用 Azure CLI 管理 Azure 磁碟](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

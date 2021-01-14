---
title: 從 Azure 下載 Windows VHD
description: 使用 Azure 入口網站來下載 Windows VHD。
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/13/2019
ms.author: cynthn
ms.openlocfilehash: 5e6f7b461d205478d6a3fe3eecbbb933d0533ec9
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98200510"
---
# <a name="download-a-windows-vhd-from-azure"></a>從 Azure 下載 Windows VHD

本文說明如何使用 Azure 入口網站，從 Azure 下載 Windows 虛擬硬碟 (VHD) 檔案。

## <a name="optional-generalize-the-vm"></a>選擇性：將 VM 一般化

如果您想要使用 VHD 作為 [映射](tutorial-custom-images.md) 來建立其他 vm，您應該使用 [Sysprep](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) 將作業系統一般化。 

若要使用 VHD 作為映射來建立其他 Vm，請將 VM 一般化。

1. 如果您尚未這麼做，請登入 [Azure 入口網站](https://portal.azure.com/)。
2. [連接至 VM](connect-logon.md)。 
3. 在 VM 上，以系統管理員身分開啟 [命令提示字元] 視窗。
4. 切換至 *%windir%\system32\sysprep* 目錄並執行 sysprep.exe。
5. 在 [系統準備工具] 對話方塊中，選取 [進入系統全新體驗 (OOBE)]，並確認已選取 [一般化]。
6. 在 [關機選項] 中選取 [ **關機**]，然後按一下 **[確定]**。 


## <a name="stop-the-vm"></a>停止 VM

如果 VHD 連接至執行中的 VM，便無法從 Azure 下載該 VHD。 您必須先停止 VM，才能下載 VHD。 

1. 在 Azure 入口網站的中樞功能表中，按一下 [虛擬機器]。
1. 從清單中選取 VM。
1. 在 VM 的刀鋒視窗中，按一下 [停止]。


## <a name="generate-download-url"></a>產生下載 URL

若要下載 VHD 檔案，您需要產生 [ (SAS) URL 的共用存取 ](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json) 簽章。 產生 URL 時，會將到期時間指派給 URL。

1. 在 VM 的頁面上，按一下左側功能表中的 [ **磁片** ]。
1. 選取 VM 的作業系統磁片。
1. 在磁片的頁面上，從左側功能表中選取 [ **磁片匯出** ]。
1. URL 的預設到期時間為 *3600* 秒。 針對 Windows OS 磁片，將此設為 **36000** 。
1. 按一下 [產生 URL]。

> [!NOTE]
> 到期時間會從預設設定增加，以提供足夠的時間來下載 Windows Server 作業系統的大型 VHD 檔案。 根據您的連線速度而定，包含 Windows Server 作業系統的 VHD 檔案可能會花數小時的時間下載。 如果您正在下載資料磁碟的 VHD，預設的時間便已足夠。 
> 
> 

## <a name="download-vhd"></a>下載 VHD

1. 在產生的 URL 之下，按一下 [下載 VHD 檔案]。
1. 您可能需要按一下瀏覽器中的 [ **儲存** ] 以開始下載。 VHD 檔案的預設名稱是 *abcd*。

## <a name="next-steps"></a>後續步驟

- 了解如何[將 VHD 檔案上傳至 Azure](upload-generalized-managed.md)。 
- [從儲存體帳戶中的非受控磁片建立受控磁片](attach-disk-ps.md)。
- [使用 PowerShell 管理 Azure 磁碟](tutorial-manage-data-disk.md)。

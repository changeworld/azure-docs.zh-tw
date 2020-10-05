---
title: 為 Azure Stack Edge Pro GPU 裝置建立 VM 映像
description: 說明如何建立 Linux 或 Windows VM 映像，以搭配您的 Azure Stack Edge Pro GPU 裝置使用。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 09/04/2020
ms.author: alkohli
ms.openlocfilehash: 745d0df07b6d0d01acf0d564df8c242e16f3f56d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "90890982"
---
# <a name="create-custom-vm-images-for-your-azure-stack-edge-pro-device"></a>為 Azure Stack Edge Pro 裝置建立自訂 VM 映像

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

若要在 Azure Stack Edge Pro 裝置上部署 VM，您必須能夠建立可用於建立 VM 的自訂 VM 映像。 本文說明建立 Linux 或 Windows VM 自訂映像所需的步驟，您可將其用於在 Azure Stack Edge Pro 裝置上部署 VM。

## <a name="vm-image-workflow"></a>VM 映像工作流程

此工作流程會要求您在 Azure 中建立虛擬機器、自訂 VM、一般化，然後下載對應至該 VM 的 VHD。 此一般化 VHD 會上傳至 Azure Stack Edge Pro、從該 VHD 建立受控磁碟、從受控磁碟建立映像，最後從該映像建立 VM。   

如需詳細資訊，請移至[使用 Azure PowerShell 在 Azure Stack Edge Pro 裝置上部署 VM](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md)。


## <a name="create-a-windows-custom-vm-image"></a>建立 Windows 自訂 VM 映像

請執行下列步驟來建立 Windows VM 映像。

1. 建立 Windows 虛擬機器。 如需詳細資訊，請移至[教學課程：使用 Azure PowerShell 建立和管理 Windows VM](../virtual-machines/windows/tutorial-manage-vm.md)

2. 下載現有的 OS 磁碟。

    - 遵循[下載 VHD](../virtual-machines/windows/download-vhd.md) 中的步驟。

    - 使用下列 `sysprep` 命令，而不是上述程序中所述的命令。
    
        `c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm`
   
       您也可以參閱 [Sysprep (系統準備) 概觀](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)。

使用此 VHD，現在即可在您的 Azure Stack Edge Pro 裝置上建立及部署 VM。

## <a name="create-a-linux-custom-vm-image"></a>建立自訂 Linux VM 映像

請執行下列步驟來建立 Linux VM 映像。

1. 建立 Linux 虛擬機器。 如需詳細資訊，請移至[教學課程：使用 Azure CLI 建立和管理 Linux VM](../virtual-machines/linux/tutorial-manage-vm.md)。

1. 取消佈建 VM。 使用 Azure VM 代理程式將電腦特定的檔案和資料刪除。 在來源 Linux VM 上使用 `waagent` 命令搭配 `-deprovision+user` 參數。 如需詳細資訊，請參閱[了解和使用 Azure Linux 代理程式](../virtual-machines/extensions/agent-linux.md)。

    1. 使用 SSH 用戶端連線到 Linux VM。
    2. 在 SSH 視窗中，輸入下列命令：
       
        ```bash
        sudo waagent -deprovision+user
        ```
       > [!NOTE]
       > 只在您要擷取作為映像的 VM 上執行這個命令。 此命令不能保證映像檔中的所有機密資訊都會清除完畢或適合轉散發。 `+user` 參數也會移除最後一個佈建的使用者帳戶。 若要在 VM 中保留使用者帳戶認證，僅使用 `-deprovision`。
     
    3. 輸入 **y** 繼續。 您可以新增 `-force` 參數，便不用進行此確認步驟。
    4. 在命令完成之後，請輸入**exit** 關閉 SSH 用戶端。  此時 VM 仍會在執行中。


1. [下載現有的 OS 磁碟](../virtual-machines/linux/download-vhd.md)。

使用此 VHD，現在即可在您的 Azure Stack Edge Pro 裝置上建立及部署 VM。 您可使用下列兩個 Azure Marketplace 映像來建立 Linux 自訂映像：

|項目名稱  |描述  |發行者  |
|---------|---------|---------|
|[Ubuntu Server](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.ubuntuserver) |Ubuntu Server 是全世界最受歡迎的雲端 Linux 環境。|Canonical|
|[Debian 8 "Jessie"](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.debian) |Debian GNU/Linux 是最受歡迎其中一個 Linux 散發套件。     |credativ|

如需可運作的 Azure Marketplace 映像完整清單 (目前尚未測試)，請移至 [Azure Stack Hub 可用的 Azure Marketplace 項目](https://docs.microsoft.com/azure-stack/operator/azure-stack-marketplace-azure-items?view=azs-1910)。


## <a name="next-steps"></a>後續步驟

[在 Azure Stack Edge Pro 裝置上部署 VM](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md)。

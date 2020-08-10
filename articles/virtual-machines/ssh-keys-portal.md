---
title: 在 Azure 入口網站中建立 SSH 金鑰
description: 瞭解如何在用於連線 Linux Vm 的 Azure 入口網站中產生和儲存 SSH 金鑰。
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 07/31/2020
ms.author: cynthn
ms.openlocfilehash: 17ab7ee75e335d686bf308c4b15a53dc4e2e6b0e
ms.sourcegitcommit: 1a0dfa54116aa036af86bd95dcf322307cfb3f83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88041743"
---
# <a name="generate-and-store-ssh-keys-in-the-azure-portal"></a>在 Azure 入口網站中產生並儲存 SSH 金鑰

如果您經常使用入口網站來部署 Linux Vm，您可以直接在入口網站中建立 SSH 金鑰，或從您的電腦上傳它們，以更輕鬆地使用它。

您可以在第一次建立 VM 時建立 SSH 金鑰，並將其重複用於其他 Vm。 或者，您可以另外建立 SSH 金鑰，以便在 Azure 中儲存一組金鑰以符合您的組織需求。 

如果您有現有的金鑰，而且想要在入口網站中簡化其使用方式，您可以上傳它們，並將它們儲存在 Azure 中以供重複使用。

如需有關建立和使用 SSH 金鑰與 Linux Vm 的詳細資訊，請參閱[使用 ssh 金鑰連接到 Linux vm](./linux/ssh-from-windows.md)。

## <a name="generate-new-keys"></a>產生新的金鑰

1. 開啟 [Azure 入口網站](https://portal.azure.com)。

1. 在頁面頂端，輸入*SSH*進行搜尋。 在 [*Marketplace*] 底下，選取 [ **SSH 金鑰**]。

1. 在 [ **SSH 金鑰**] 頁面上，選取 [**建立**]。

   :::image type="content" source="./media/ssh-keys/portal-sshkey.png" alt-text="建立新的資源群組，並產生 SSH 金鑰組":::

1. 在 [**資源群組**] 中 **，選取 [新建]** 以建立新的資源群組來儲存您的金鑰。 輸入資源群組的名稱，然後選取 **[確定]**。

1. 在 [**區域**] 中，選取要儲存金鑰的區域。 您可以使用任何區域中的金鑰，這只是將儲存它們的區域。

1. 在 [**金鑰組名稱**] 中，輸入金鑰的名稱。

1. 在 [ **SSH 公開金鑰來源**] 中，選取 [**產生公開金鑰來源**]。 

1. 當您完成時，選取 [檢閱 + 建立]。

1. 通過驗證之後，選取 [建立]。

1. 接著，您將會看到快顯視窗，請選取 [**下載私密金鑰] 和 [建立資源**]。 這會下載 SSH 金鑰做為 pem 檔案。

   :::image type="content" source="./media/ssh-keys/download-key.png" alt-text="以 pem 檔案的形式下載私密金鑰":::

1. 下載 pem 檔案之後，您可能會想要將它移到您電腦上的某個位置，以便從 SSH 用戶端輕鬆地指向該檔案。


## <a name="connect-to-the-vm"></a>連接至 VM

在您的本機電腦上，開啟 PowerShell 提示字元，然後輸入：

```powershell
ssh -i <path to the .pem file> username@<ipaddress of the VM>
```

例如，輸入：`ssh -i /Downloads/mySSHKey.pem azureuser@123.45.67.890`


## <a name="upload-an-ssh-key"></a>上傳 SSH 金鑰

您也可以上傳公開 SSH 金鑰，以儲存在 Azure 中。 如需如何建立 SSH 金鑰組的相關資訊，請參閱[使用 ssh 金鑰連線至 Linux vm](./linux/ssh-from-windows.md)。

1. 開啟 [Azure 入口網站](https://portal.azure.com)。

1. 在頁面頂端，輸入*SSH*進行搜尋。 在 [*Marketplace*] 底下，選取 [ **SSH 金鑰**]。

1. 在 [ **SSH 金鑰**] 頁面上，選取 [**建立**]。

   :::image type="content" source="./media/ssh-keys/upload.png" alt-text="上傳要儲存在 Azure 中的 SSH 公開金鑰":::

1. 在 [**資源群組**] 中 **，選取 [新建]** 以建立新的資源群組來儲存您的金鑰。 輸入資源群組的名稱，然後選取 **[確定]**。

1. 在 [**區域**] 中，選取要儲存金鑰的區域。 您可以使用任何區域中的金鑰，這只是將儲存它們的區域。

1. 在 [**金鑰組名稱**] 中，輸入金鑰的名稱。

1. 在 [ **SSH 公開金鑰來源**] 中，選取 **[上傳現有的公開金鑰**]。 

1. 將公開金鑰的完整內容貼入 **[上傳金鑰**]，然後選取 [**審核 + 建立**]。

1. 驗證完成時，選取 [建立]。 

上傳金鑰後，您就可以在建立 VM 時選擇使用它。

## <a name="next-steps"></a>後續步驟

若要深入瞭解如何搭配使用 SSH 金鑰與 Azure Vm，請參閱[使用 ssh 金鑰連線至 Linux vm](./linux/ssh-from-windows.md)。

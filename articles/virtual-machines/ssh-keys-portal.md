---
title: 在 Azure 入口網站中建立 SSH 金鑰
description: 瞭解如何在連接 Linux Vm 的 Azure 入口網站中產生並儲存 SSH 金鑰。
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 08/25/2020
ms.author: cynthn
ms.openlocfilehash: abc9a2ae130d987c90ce87ffaecbf2bb44b06010
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88929428"
---
# <a name="generate-and-store-ssh-keys-in-the-azure-portal"></a>在 Azure 入口網站中產生並儲存 SSH 金鑰

如果您經常使用入口網站來部署 Linux Vm，您可以直接在入口網站中建立 SSH 金鑰，或從電腦上傳它們，以簡化使用 SSH 金鑰。

您可以在第一次建立 VM 時建立 SSH 金鑰，並將其重複用於其他 Vm。 或者，您也可以分別建立 SSH 金鑰，讓您有一組儲存在 Azure 中的金鑰，以符合您的組織需求。 

如果您有現有的金鑰，而且想要在入口網站中簡化使用它們，您可以上傳它們，並將它們儲存在 Azure 中以供重複使用。

如需有關建立和使用 SSH 金鑰與 Linux Vm 的詳細資訊，請參閱 [使用 ssh 金鑰連接到 Linux vm](./linux/ssh-from-windows.md)。

## <a name="generate-new-keys"></a>產生新的金鑰

1. 開啟 [Azure 入口網站](https://portal.azure.com)。

1. 在頁面頂端，輸入 *SSH* 以搜尋。 在 [ **Marketplace**] 下，選取 [ **SSH 金鑰**]。

1. 在 [ **SSH 金鑰** ] 頁面上，選取 [ **建立**]。

   :::image type="content" source="./media/ssh-keys/portal-sshkey.png" alt-text="建立新的資源群組，並產生 SSH 金鑰組":::

1. 在 **資源群組** 中，選取 [ **建立新** 的] 以建立新的資源群組來儲存您的金鑰。 輸入資源群組的名稱，然後選取 **[確定]**。

1. 在 [ **區域** ] 中，選取要儲存金鑰的區域。 您可以使用任何區域中的金鑰，而這只是儲存它們的區域。

1. 在 **金鑰組名稱**中輸入金鑰的名稱。

1. 在 [ **SSH 公開金鑰來源**] 中，選取 [ **產生公開金鑰來源**]。 

1. 當您完成時，選取 [檢閱 + 建立]。

1. 通過驗證之後，選取 [建立]。

1. 然後，您會看到一個快顯視窗，選取 [ **下載私密金鑰] 和 [建立資源**]。 這會將 SSH 金鑰下載為 pem 檔案。

   :::image type="content" source="./media/ssh-keys/download-key.png" alt-text="建立新的資源群組，並產生 SSH 金鑰組":::

1. 下載 pem 檔案之後，您可能會想要將它移到您電腦上的某個位置，以便從 SSH 用戶端輕鬆地指向該檔案。


## <a name="connect-to-the-vm"></a>連接至 VM

在您的本機電腦上，開啟 PowerShell 提示字元，然後輸入：

```powershell
ssh -i <path to the .pem file> username@<ipaddress of the VM>
```

例如，輸入：`ssh -i /Downloads/mySSHKey.pem azureuser@123.45.67.890`


## <a name="upload-an-ssh-key"></a>上傳 SSH 金鑰

您也可以上傳要儲存在 Azure 中的公開 SSH 金鑰。 如需有關如何建立 SSH 金鑰組的詳細資訊，請參閱 [使用 ssh 金鑰連接到 Linux vm](./linux/ssh-from-windows.md)。

1. 開啟 [Azure 入口網站](https://portal.azure.com)。

1. 在頁面頂端，輸入 *SSH* 以搜尋。 在 [*Marketplace*] 底下，選取 [ **SSH 金鑰**]。

1. 在 [ **SSH 金鑰** ] 頁面上，選取 [ **建立**]。

   :::image type="content" source="./media/ssh-keys/upload.png" alt-text="建立新的資源群組，並產生 SSH 金鑰組":::

1. 在 **資源群組** 中，選取 [ **建立新** 的] 以建立新的資源群組來儲存您的金鑰。 輸入資源群組的名稱，然後選取 **[確定]**。

1. 在 [ **區域** ] 中，選取要儲存金鑰的區域。 您可以使用任何區域中的金鑰，而這只是儲存它們的區域。

1. 在 **金鑰組名稱**中輸入金鑰的名稱。

1. 在 [ **SSH 公開金鑰來源**] 中，選取 **[上傳現有的公開金鑰**]。 

1. 將公開金鑰的完整內容貼到 **上傳金鑰** ，然後選取 [ **審核 + 建立**]。

1. 驗證完成時，選取 [建立]。 

上傳金鑰之後，您可以在建立 VM 時選擇使用它。

## <a name="list-keys"></a>列出金鑰

在入口網站中建立的 SSH 金鑰會儲存為資源，因此您可以篩選資源檢視器以查看所有資源。

1. 在入口網站中，選取 [ **所有資源**]。
1. 在 [篩選器] 中，選取 [ **類型**]，取消選取 [ **全選** ] 選項以清除清單。
1. 在篩選器中輸入 **ssh** ，然後選取 **ssh 金鑰**。

   :::image type="content" source="./media/ssh-keys/filter.png" alt-text="建立新的資源群組，並產生 SSH 金鑰組":::

## <a name="get-the-public-key"></a>取得公開金鑰

如果您需要公開金鑰，您可以從入口網站頁面輕鬆地複製金鑰。 您只需使用上一節中的程式來列出您的金鑰 () 然後從清單中選取索引鍵。 您的金鑰頁面將會開啟，而且您可以按一下金鑰旁的 [ **複製到剪貼** 簿] 圖示來複製它。

## <a name="next-steps"></a>後續步驟

若要深入瞭解如何搭配使用 SSH 金鑰與 Azure Vm，請參閱 [使用 ssh 金鑰連接到 Linux vm](./linux/ssh-from-windows.md)。

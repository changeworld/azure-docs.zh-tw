---
title: 上傳或複製具有 Azure CLI 的自訂 Linux VM
description: 使用 Resource Manager 部署模型與 Azure CLI 來上傳或複製自訂虛擬機器
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/10/2019
ms.author: cynthn
ms.openlocfilehash: 3306647078c46a7c66b3d7b257b213c7a48e690d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81460421"
---
# <a name="create-a-linux-vm-from-a-custom-disk-with-the-azure-cli"></a>使用 Azure CLI 從自訂磁碟建立 Linux VM

<!-- rename to create-vm-specialized -->

本文示範如何上傳自訂虛擬硬碟 (VHD)，以及如何在 Azure 中複製現有的 VHD。 新建立的 VHD 接著可用來建立新的 Linux 虛擬機器 (VM)。 您可以安裝並設定 Linux 發行版本以符合您的需求，然後使用該 VHD 來建立新的 Azure 虛擬機器。

若要從自訂磁碟建立多個 VM，先從您的 VM 或 VHD 建立映像。 如需詳細資訊，請參閱[使用 CLI 建立 Azure VM 的自訂映像](tutorial-custom-images.md)。

您有兩個選項可用來建立自訂磁碟：
* 上傳 VHD
* 複製現有的 Azure VM


## <a name="requirements"></a>需求
若要完成下列步驟，您將需要：

- 一部已準備好用於 Azure 中的 Linux 虛擬機器。 本文的[準備 VM](#prepare-the-vm) 一節將說明如何尋找關於安裝 Azure Linux 代理程式 (waagent) 的發行版本特有資訊，您需要有此資訊，才能使用 SSH 連線到至 VM。
- 以 VHD 格式從現有[經 Azure 背書的 Linux 發行版本](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (或者，請參閱[非背書發行版本的資訊](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) 到虛擬磁碟的 VHD 檔案。 有多項工具可用來建立 VM 和 VHD：
  - 安裝和設定 [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) 或 [KVM](https://www.linux-kvm.org/page/RunningKVM)，並小心使用 VHD 做為您的映像格式。 如有需要，您可以使用 `qemu-img convert` 來[轉換映像](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) \(英文\)。
  - 您也可以在 [Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) 或 [Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx) 上使用 Hyper-V。

> [!NOTE]
> Azure 不支援較新的 VHDX 格式。 當您建立 VM 時，請指定 VHD 做為格式。 如有需要，您可以使用 [qemu-img convert](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) \(英文\) 或 [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) PowerShell Cmdlet，將 VHDX 磁碟轉換為 VHD。 Azure 不支援上傳動態 VHD，因此您必須將這類磁碟轉換為靜態 VHD 之後再上傳。 您可以在將它們上傳至 Azure 的期間，使用 [適用於 GO 的 Azure VHD 公用程式](https://github.com/Microsoft/azure-vhd-utils-for-go) \(英文\) 之類的工具來轉換動態磁碟。
> 
> 


- 確定您已安裝最新的 [Azure CLI](/cli/azure/install-az-cli2)，並且已使用 [az login](/cli/azure/reference-index#az-login) 登入 Azure 帳戶。

在下列範例中，以您自己的值取代範例參數名稱，例如`myResourceGroup`、 `mystorageaccount`和`mydisks`。

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>準備 VM

Azure 支援各種 Linux 散發套件 (請參閱 [背書的散發套件](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json))。 下列文章將說明如何準備 Azure 上支援的各種 Linux 發行版本：

* [CentOS 型發行版本](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SLES 和 openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [其他：非背書的發行版本](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

如需有關為 Azure 準備 Linux 映像的更多一般秘訣，另請參閱 [Linux 安裝注意事項](create-upload-generic.md#general-linux-installation-notes)。

> [!NOTE]
> 只有在搭配[經 Azure 背書之 Linux 發行版本](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中＜支援的版本＞下指定的設定詳細資料來使用其中一個經背書的發行版本時，[Azure 平台 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) 才適用於執行 Linux 的 VM。
> 
> 

## <a name="option-1-upload-a-vhd"></a>選項 1：上傳 VHD

您現在可以直接將 VHD 上傳至受控磁片。 如需指示，請參閱[使用 Azure CLI 將 VHD 上傳至 Azure](disks-upload-vhd-to-managed-disk-cli.md)。

## <a name="option-2-copy-an-existing-vm"></a>選項 2：複製現有的 VM

您可以也在 Azure 中建立自訂的 VM，然後複製 OS 磁碟並將它連結到新的 VM，以建立另一個複本。 這適合用來測試，但如果您想要使用現有的 Azure VM 作為多個新 VM 的模型，請建立「映像」**。 如需如何從現有 Azure VM 建立映像的詳細資訊，請參閱[使用 CLI 建立 Azure VM 的自訂映像](tutorial-custom-images.md)。

如果您想要將現有的 VM 複製到另一個區域，您可能會想要使用 azcopy 來[建立另一個區域中的磁片複本](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk)。 

否則，您應該製作 VM 的快照集，然後從快照集建立新的 OS VHD。

### <a name="create-a-snapshot"></a>建立快照集

這個範例會在名為 myResourceGroup** 資源群組中建立名為 myVM** 的 VM 快照集，並建立名為 osDiskSnapshot** 的快照集。

```azurecli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDiskSnapshot
```
###  <a name="create-the-managed-disk"></a>建立受控磁碟

從快照集建立新的受控磁碟。

取得快照集的識別碼。 在此範例中，快照集的名稱為 osDiskSnapshot** 且位於 myResourceGroup** 資源群組中。

```azurecli
snapshotId=$(az snapshot show --name osDiskSnapshot --resource-group myResourceGroup --query [id] -o tsv)
```

建立受控磁碟。 在此範例中，我們將從快照集中建立名為 *myManagedDisk* 的受控磁碟，此磁碟位於標準儲存體且大小為 128 GB。

```azurecli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>建立 VM

使用 [az vm create](/cli/azure/vm#az-vm-create) 建立您的 VM，並連結 (--attach-os-disk) 受控磁碟作為 OS 磁碟。 下列範例會使用從您上傳之 VHD 建立的受控磁碟，來建立名為 *myNewVM* 的 VM：

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNewVM \
    --os-type linux \
    --attach-os-disk myManagedDisk
```

您應該能夠使用來源 VM 的認證，SSH 到 VM。 

## <a name="next-steps"></a>後續步驟
在您備妥並上傳自訂虛擬磁碟之後，您可以深入了解如何 [使用 Resource Manager 和範本](../../azure-resource-manager/management/overview.md)。 您也可以 [將資料磁碟新增](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 至您的新 VM。 如果您需要存取在您的 VM 上執行的應用程式，請務必 [開啟連接埠和端點](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

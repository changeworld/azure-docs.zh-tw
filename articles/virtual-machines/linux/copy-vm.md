---
title: 使用 Azure CLI 複製 Linux VM
description: 了解如何使用 Azure CLI 和受控磁碟來建立 Azure Linux VM 的複本。
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 10/17/2018
ms.author: cynthn
ms.custom: legacy
ms.openlocfilehash: 406eda6902ae451c7fdf79e1bd1215c035a66750
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82792219"
---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-and-managed-disks"></a>使用 Azure CLI 和受控磁碟來建立 Azure Linux VM 的複本

本文說明如何使用 Azure CLI 來建立執行 Linux 的 Azure 虛擬機器（VM）複本。 若要大規模複製、建立、儲存及共用 VM 映射，請參閱[共用映射資源庫](shared-images.md)。

您也可以[上傳 VHD 並從中建立 VM](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

## <a name="prerequisites"></a>必要條件

-   安裝[Azure CLI](/cli/azure/install-az-cli2)。

-   使用 [az login](/cli/azure/reference-index#az-login) 登入 Azure 帳戶。

-   讓 Azure VM 做為複製的來源。

## <a name="stop-the-source-vm"></a>停止來源 VM

使用 [az vm deallocate](/cli/azure/vm#az-vm-deallocate) 解除配置來源 VM。
下列範例會解除配置 *myResourceGroup* 資源群組中名為 *myVM* 的 VM：

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

## <a name="copy-the-source-vm"></a>複製來源 VM

若要複製 VM，您可建立基礎虛擬硬碟的複本。 此程序會建立特製化虛擬硬碟 (VHD) 作為受控磁碟，其包含與來源 VM 相同的組態和設定。

如需 Azure 受控磁碟的詳細資訊，請參閱 [Azure 受控磁碟概觀](../windows/managed-disks-overview.md)。 

1.  使用 [az vm list](/cli/azure/vm#az-vm-list) 列出每部 VM 及其 OS 磁碟的名稱。 下列範例會列出名為 *myResourceGroup* 的資源群組中的所有 VM：
    
    ```azurecli
    az vm list -g myResourceGroup \
         --query '[].{Name:name,DiskName:storageProfile.osDisk.name}' \
         --output table
    ```

    輸出類似於下列範例：

    ```azurecli
    Name    DiskName
    ------  --------
    myVM    myDisk
    ```

1.  透過建立新的受控磁碟及使用 [az disk create](/cli/azure/disk#az-disk-create) 來複製磁碟。 下列範例會從名為 *myDisk* 的受控磁碟建立名為 *myCopiedDisk* 的磁碟：

    ```azurecli
    az disk create --resource-group myResourceGroup \
         --name myCopiedDisk --source myDisk
    ``` 

1.  使用 [az disk list](/cli/azure/disk#az-disk-list) 來確認此受控磁碟現在位於您的資源群組中。 下列範例會列出名為 *myResourceGroup* 的資源群組中的受控磁碟：

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```


## <a name="set-up-a-virtual-network"></a>設定虛擬網路

下列選擇性步驟會建立新的虛擬網路、子網路、公用 IP 位址和虛擬網路介面卡 (NIC)。

如果您為了進行疑難排解或其他部署而複製 VM，您可能不想使用現有虛擬網路中的 VM。

如果您想為所複製的 VM 建立虛擬網路基礎結構，請遵循接下來的幾個步驟。 如果不想建立虛擬網路，請跳至[建立 VM](#create-a-vm)。

1.  使用 [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) 建立虛擬網路。 下列範例會建立名為 *myVnet* 的虛擬網路和名為 *mySubnet* 的子網路：

    ```azurecli
    az network vnet create --resource-group myResourceGroup \
        --location eastus --name myVnet \
        --address-prefix 192.168.0.0/16 \
        --subnet-name mySubnet \
        --subnet-prefix 192.168.1.0/24
    ```

1.  使用 [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) 建立公用 IP。 下列範例會建立名為 *myPublicIP* 的公用 IP，其 DNS 名稱為 *mypublicdns*。 (由於 DNS 名稱必須是唯一的，請提供唯一的名稱)。

    ```azurecli
    az network public-ip create --resource-group myResourceGroup \
        --location eastus --name myPublicIP --dns-name mypublicdns \
        --allocation-method static --idle-timeout 4
    ```

1.  使用 [az network nic create](/cli/azure/network/nic#az-network-nic-create) 來建立 NIC。
    下列範例會建立連結至 *mySubnet* 子網路且名為 *myNic* 的 NIC：

    ```azurecli
    az network nic create --resource-group myResourceGroup \
        --location eastus --name myNic \
        --vnet-name myVnet --subnet mySubnet \
        --public-ip-address myPublicIP
    ```

## <a name="create-a-vm"></a>建立 VM

使用 [az vm create](/cli/azure/vm#az-vm-create) 建立 VM。

指定要做為 OS 磁碟的所複製受控磁碟 (`--attach-os-disk`)，如下所示︰

```azurecli
az vm create --resource-group myResourceGroup \
    --name myCopiedVM --nics myNic \
    --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>後續步驟

以瞭解如何使用[共用映射資源庫](shared-images.md)來管理 VM 映射。

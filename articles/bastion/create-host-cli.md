---
title: 使用 Azure CLI 建立堡壘主機 |Azure 防禦
description: 在本文中，您將瞭解如何建立和刪除堡壘主機
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/13/2020
ms.author: cherylmc
ms.openlocfilehash: 851ec86feb5244ff43759a7aef2b80876dcfa734
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92018537"
---
# <a name="create-an-azure-bastion-host-using-azure-cli"></a>使用 Azure CLI 建立 Azure 堡壘主機

本文說明如何使用 Azure CLI 建立 Azure 堡壘主機。 部署防禦之後，您可以透過瀏覽器使用 Azure 入口網站，透過其私人 IP 位址連線到 VM。 您的 VM 不需要公用 IP 位址、額外的用戶端或特殊軟體。 Azure Bastion 部署依虛擬網路來進行，而非以訂用帳戶/帳戶或虛擬機器為依據。 在相同虛擬網路中的所有 Vm 皆可使用順暢的 RDP/SSH 體驗。

（選擇性）您可以使用 [Azure 入口網站](tutorial-create-host-portal.md)建立 Azure 堡壘主機，或使用 [Azure PowerShell](bastion-create-host-powershell.md)。

## <a name="before-you-begin"></a>開始之前

請確認您有 Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，您可以啟用 [MSDN 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) 或註冊 [免費帳戶](https://azure.microsoft.com/pricing/free-trial)。

[!INCLUDE [Cloud Shell CLI](../../includes/vpn-gateway-cloud-shell-cli.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>建立 Bastion 主機

本節可協助您使用 Azure CLI 建立新的 Azure 防禦資源。

> [!NOTE]
> 如範例所示， `--location` `--resource-group` 針對每個命令使用參數，以確保資源會一起部署。

1. 建立虛擬網路和 Azure 堡壘子網。 您必須使用 [名稱] 值 **AzureBastionSubnet**來建立 Azure 堡壘子網。 此值可讓 Azure 知道要將 Bastion 資源部署到哪一個子網路。 這與閘道子網路不同。 您必須使用至少/27 或更大子網的子網 (/27、/26 等) 。 建立沒有任何路由表或委派的 **AzureBastionSubnet** 。 如果您使用 **AzureBastionSubnet**上的網路安全性群組，請參閱「 [使用 nsg](bastion-nsg.md) 」一文。

   ```azurecli-interactive
   az network vnet create --resource-group MyResourceGroup --name MyVnet --address-prefix 10.0.0.0/16 --subnet-name AzureBastionSubnet --subnet-prefix 10.0.0.0/24 --location northeurope
   ```

2. 建立 Azure 防禦的公用 IP 位址。 公用 IP 是公用 IP 位址，RDP/SSH 會透過埠 443)  (存取 RDP/SSH。 公用 IP 位址必須與您建立的 Bastion 資源位於相同的區域中。

   ```azurecli-interactive
   az network public-ip create --resource-group MyResourceGroup --name MyIp --sku Standard --location northeurope
   ```

3. 在虛擬網路的 AzureBastionSubnet 中建立新的 Azure 防禦資源。 防禦資源建立和部署大約需要5分鐘的時間。

   ```azurecli-interactive
   az network bastion create --name MyBastion --public-ip-address MyIp --resource-group MyResourceGroup --vnet-name MyVnet --location northeurope
   ```

## <a name="next-steps"></a>後續步驟

* 連接至虛擬機器。
   * [Linux VM](bastion-connect-vm-ssh.md)
   * [Windows VM](bastion-connect-vm-rdp.md)


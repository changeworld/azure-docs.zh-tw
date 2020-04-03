---
title: 使用 Azure CLI 建立堡壘主機 |Azure 堡壘
description: 在本文中,瞭解如何建立和刪除堡壘主機
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mialdrid
ms.openlocfilehash: f98c965ad3b776f3688a716ba28b5367a00c9119
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619230"
---
# <a name="create-an-azure-bastion-host-using-azure-cli"></a>使用 Azure CLI 建立 Azure 堡壘主機

本文介紹如何使用 Azure CLI 創建 Azure 堡壘主機。 在虛擬網路中預配 Azure Bastion 服務後,同一虛擬網路中的所有 VM 都可以使用無縫的 RDP/SSH 體驗。 Azure Bastion 部署依虛擬網路來進行，而非以訂用帳戶/帳戶或虛擬機器為依據。

或者,可以使用[Azure 門戶](bastion-create-host-portal.md)或使用[Azure PowerShell](bastion-create-host-powershell.md)創建 Azure 堡壘主機。

## <a name="before-you-begin"></a>開始之前

請確認您有 Azure 訂用帳戶。 如果您還沒有 Azure 訂閱,則可以啟動[MSDN 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)或註冊[免費帳戶](https://azure.microsoft.com/pricing/free-trial)。

[!INCLUDE [cloudshell cli](../../includes/vpn-gateway-cloud-shell-cli.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>建立 Bastion 主機

本節可説明您使用 Azure CLI 創建新的 Azure 堡壘資源。

1. 創建虛擬網路和 Azure 堡壘子網。 您必須使用名稱值**Azure Bastion 子網**創建 Azure 堡壘子網。 此值使 Azure 知道要將堡壘資源部署到哪個子網。 這與閘道網不同。 您必須使用至少 /27 或更大的子網(/27、/26 等)的子網。 創建**AzureBastion Subnet,** 沒有任何路由表或授權。 如果在**AzureBastionSubnet**上使用網路安全組,請參閱[使用 NSG](bastion-nsg.md)的文章。

   ```azurecli-interactive
   az network vnet create -g MyResourceGroup -n MyVnet --address-prefix 10.0.0.0/16 --subnet-name AzureBastionSubnet  --subnet-prefix 10.0.0.0/24
   ```

2. 為 Azure 堡壘創建公共 IP 位址。 公共 IP 是公共 IP 位址,是將訪問 RDP/SSH 的堡壘資源(通過埠 443)。 公共 IP 位址必須與您正在創建的堡壘資源位於同一區域。

   ```azurecli-interactive
   az network public-ip create -g MyResourceGroup -n MyIp
   ```

3. 在虛擬網路的 Azure Bastion Subnet 建立新的 Azure 堡壘資源。 創建和部署堡壘資源大約需要5分鐘。

   ```azurecli-interactive
   az network bastion create --name $name--public-ip-address $publicip--resource-group $RgName --vnet-name $VNetName --location $location
                           
   ```

## <a name="next-steps"></a>後續步驟

* 有關詳細資訊,請閱讀[堡壘常見問題解答](bastion-faq.md)。

* 要將網路安全組與 Azure 堡壘子網一起使用,請參閱[使用 NSG。](bastion-nsg.md)

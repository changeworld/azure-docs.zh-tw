---
title: 使用 Azure CLI 建立防禦主機 |Azure 防禦
description: 在本文中，您將瞭解如何建立和刪除防禦主機
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mialdrid
ms.openlocfilehash: f98c965ad3b776f3688a716ba28b5367a00c9119
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80619230"
---
# <a name="create-an-azure-bastion-host-using-azure-cli"></a>使用 Azure CLI 建立 Azure 防禦主機

本文說明如何使用 Azure CLI 建立 Azure 防禦主機。 一旦在您的虛擬網路中布建 Azure 防禦服務，相同虛擬網路中的所有 Vm 都可以使用順暢的 RDP/SSH 體驗。 Azure Bastion 部署依虛擬網路來進行，而非以訂用帳戶/帳戶或虛擬機器為依據。

（選擇性）您可以使用[Azure 入口網站](bastion-create-host-portal.md)或使用[Azure PowerShell](bastion-create-host-powershell.md)來建立 Azure 防禦主機。

## <a name="before-you-begin"></a>開始之前

請確認您有 Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，您可以啟用[MSDN 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)或註冊[免費帳戶](https://azure.microsoft.com/pricing/free-trial)。

[!INCLUDE [cloudshell cli](../../includes/vpn-gateway-cloud-shell-cli.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>建立 Bastion 主機

本節可協助您使用 Azure CLI 建立新的 Azure 防禦資源。

1. 建立虛擬網路和 Azure 防禦子網。 您必須使用 [名稱] 值**AzureBastionSubnet**來建立 Azure 防禦子網。 此值可讓 Azure 知道要將防禦資源部署到哪一個子網。 這與閘道子網不同。 您必須使用至少為/27 或更大子網的子網（/27、/26 等等）。 建立不含任何路由表或委派的**AzureBastionSubnet** 。 如果您在**AzureBastionSubnet**上使用網路安全性群組，請參閱[使用 nsg](bastion-nsg.md)一文。

   ```azurecli-interactive
   az network vnet create -g MyResourceGroup -n MyVnet --address-prefix 10.0.0.0/16 --subnet-name AzureBastionSubnet  --subnet-prefix 10.0.0.0/24
   ```

2. 建立 Azure 防禦的公用 IP 位址。 公用 IP 是公用 IP 位址，即會存取 RDP/SSH 的防禦資源（透過埠443）。 公用 IP 位址必須與您建立的防禦資源位於相同的區域中。

   ```azurecli-interactive
   az network public-ip create -g MyResourceGroup -n MyIp
   ```

3. 在您的虛擬網路 AzureBastionSubnet 中建立新的 Azure 防禦資源。 建立和部署防禦資源需要大約5分鐘的時間。

   ```azurecli-interactive
   az network bastion create --name $name--public-ip-address $publicip--resource-group $RgName --vnet-name $VNetName --location $location
                           
   ```

## <a name="next-steps"></a>後續步驟

* 如需其他資訊，請閱讀防禦[常見問題](bastion-faq.md)。

* 若要搭配使用網路安全性群組與 Azure 防禦子網，請參閱使用[nsg](bastion-nsg.md)。

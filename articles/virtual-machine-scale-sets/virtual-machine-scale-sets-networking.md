---
title: Azure 虛擬機器擴展集的網路
description: 如何為 Azure 虛擬機器擴展集設定一些更先進的網路屬性。
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: networking
ms.date: 06/25/2020
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: f63bcbb26752dc787d508260cce0b0518cdc7c38
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87080398"
---
# <a name="networking-for-azure-virtual-machine-scale-sets"></a>Azure 虛擬機器擴展集的網路

透過入口網站部署 Azure 虛擬機器擴展集時，特定的網路屬性為預設，例如具有輸入 NAT 規則的 Azure Load Balancer。 本文說明如何使用某些您可以使用擴展集設定的更進階網路功能。

本文所討論的所有概念都可以使用 Azure Resource Manager 範本來設定。 選取的功能也會包含 Azure CLI 和 PowerShell 範例。

## <a name="accelerated-networking"></a>加速網路
Azure 加速網路可以對虛擬機器啟用 Single Root I/O Virtualization (SR-IOV)，大幅提升網路效能。 若要深入了解如何使用加速網路，請參閱 [Windows](../virtual-network/create-vm-accelerated-networking-powershell.md) 或 [Linux](../virtual-network/create-vm-accelerated-networking-cli.md) 虛擬機器的加速網路。 若要搭配擴展集使用加速的網路，請在擴展集的 networkInterfaceConfigurations 設定中，將 enableAcceleratedNetworking 設為 **true**。 例如:

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
    {
      "name": "niconfig1",
      "properties": {
        "primary": true,
        "enableAcceleratedNetworking" : true,
        "ipConfigurations": [
          ...
        ]
      }
    }
   ]
}
```

## <a name="azure-virtual-machine-scale-sets-with-azure-load-balancer"></a>具有 Azure Load Balancer 的 Azure 虛擬機器擴展集

使用虛擬機器擴展集和負載平衡器時，應該考慮下列專案：

* **多個虛擬機器擴展集不能使用相同的負載平衡器**。
* **埠轉送和輸入 NAT 規則**：
  * 每個虛擬機器擴展集都必須有輸入 NAT 規則。
  * 建立擴展集之後，無法修改負載平衡器健全狀況探查所使用的負載平衡規則的後端埠。 若要變更埠，您可以藉由更新 Azure 虛擬機器擴展集來移除健康情況探查、更新埠，然後再次設定健康情況探查。
  * 在負載平衡器的後端集區中使用虛擬機器擴展集時，會自動建立預設的輸入 NAT 規則。
* **輸入 NAT 集**區：
  * 輸入 NAT 集區是輸入 NAT 規則的集合。 其中一個輸入 NAT 集區無法支援多個虛擬機器擴展集。
* **負載平衡規則**：
  * 在負載平衡器後端集區中使用虛擬機器擴展集時，會自動建立預設的負載平衡規則。
* **輸出規則**：
  *  若要建立負載平衡規則已參考之後端集區的輸出規則，您必須先在建立輸入負載平衡規則時，將入口網站中的 **[建立隱含輸出規則]** 標示為 [**否**]。

  :::image type="content" source="./media/vmsslb.png" alt-text="建立負載平衡規則" border="true":::

下列方法可用於部署具有現有 Azure 負載平衡器的虛擬機器擴展集。

* [使用 Azure 入口網站，設定具有現有 Azure Load Balancer 的虛擬機器擴展集](../load-balancer/configure-vm-scale-set-portal.md)。
* [使用 Azure PowerShell，設定具有現有 Azure Load Balancer 的虛擬機器擴展集](../load-balancer/configure-vm-scale-set-powershell.md)。
* [使用 Azure CLI，設定具有現有 Azure Load Balancer 的虛擬機器擴展集](../load-balancer/configure-vm-scale-set-cli.md)。

## <a name="create-a-scale-set-that-references-an-application-gateway"></a>建立參考應用程式閘道的擴展集
若要建立使用應用程式閘道的擴展集，請和此 ARM 範本設定中一樣，參考擴展集 ipConfigurations 區段中的應用程式閘道後端位址集區：

```json
"ipConfigurations": [{
  "name": "{config-name}",
  "properties": {
  "subnet": {
    "id": "{subnet-id}"
  },
  "ApplicationGatewayBackendAddressPools": [{
    "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/applicationGateways/{gateway-name}/backendAddressPools/{pool-name}"
  }]
}]
```

>[!NOTE]
> 請注意，應用程式閘道必須和擴展集位於相同的虛擬網路中，但必須與擴展集位於不同的子網路。


## <a name="configurable-dns-settings"></a>可設定的 DNS 設定
根據預設，擴展集會採取 VNET 和它們建立於該子網路的特定 DNS 設定。 不過，您可以直接設定擴展集的 DNS 設定。

### <a name="creating-a-scale-set-with-configurable-dns-servers"></a>使用可設定的 DNS 伺服器建立擴展集
若要使用 Azure CLI 搭配自訂的 DNS 設定建立擴展集，將 **--dns-servers** 引數新增至 **vmss create** 命令，後面接以空格分隔的伺服器 IP 位址。 例如:

```bash
--dns-servers 10.0.0.6 10.0.0.5
```

若要在 Azure 範本中設定自訂的 DNS 伺服器，請將 dnsSettings 屬性新增至擴展集 networkInterfaceConfigurations 區段。 例如:

```json
"dnsSettings":{
    "dnsServers":["10.0.0.6", "10.0.0.5"]
}
```

### <a name="creating-a-scale-set-with-configurable-virtual-machine-domain-names"></a>使用可設定的虛擬機器網域名稱建立擴展集
若要使用 CLI 搭配自訂的虛擬機器 DNS 名稱建立擴展集，將 **--vm-domain-name** 引數新增至 **virtual machine scale set create** 命令，後面接著代表網域名稱的字串。

若要在 Azure 範本中設定網域名稱，請將 **dnsSettings** 屬性新增至擴展集 **networkInterfaceConfigurations** 區段。 例如:

```json
"networkProfile": {
  "networkInterfaceConfigurations": [
    {
    "name": "nic1",
    "properties": {
      "primary": true,
      "ipConfigurations": [
      {
        "name": "ip1",
        "properties": {
          "subnet": {
            "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
          },
          "publicIPAddressconfiguration": {
            "name": "publicip",
            "properties": {
            "idleTimeoutInMinutes": 10,
              "dnsSettings": {
                "domainNameLabel": "[parameters('vmssDnsName')]"
              }
            }
          }
        }
      }
    ]
    }
}
```

個別虛擬機器 DNS 名稱的輸出格式如下：

```output
<vm><vmindex>.<specifiedVmssDomainNameLabel>
```

## <a name="public-ipv4-per-virtual-machine"></a>每個虛擬機器的公用 IPv4
一般情況下，Azure 擴展集虛擬機器不需要自己的公用 IP 位址。 在大部分情況下，將公用 IP 位址與負載平衡器或個別虛擬機器（也稱為 jumpbox）建立關聯，然後視需要將連入連線路由至擴展集虛擬機器（例如，透過輸入 NAT 規則），會比較經濟且安全。

但是，某些情況會要求擴展集虛擬機器具備自己的公用 IP 位址。 例如遊戲，其中主控台需要直接連線至雲端虛擬機器，進而執行遊戲實體流程。 另一個範例是虛擬機器需要對另一個分散式資料庫中跨區域的虛擬機器進行外部連線。

### <a name="creating-a-scale-set-with-public-ip-per-virtual-machine"></a>使用公用 IP 每虛擬機器建立擴展集
若要使用 CLI 建立將公用 IP 位址指派給每個虛擬機器的擴展集，請將 **--public-ip-per-vm** 參數新增至 **vmss create** 命令。 

若要使用 Azure 範本建立擴展集，請確定 Microsoft.Compute/virtualMachineScaleSets 資源的 API 版本至少為 **2017-03-30**，並將 **publicIpAddressConfiguration** JSON 屬性新增至擴展集 ipConfigurations 區段。 例如:

```json
"publicIpAddressConfiguration": {
    "name": "pub1",
    "properties": {
      "idleTimeoutInMinutes": 15
    }
}
```

範本範例：[201-vmss-public-ip-linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-public-ip-linux)

### <a name="querying-the-public-ip-addresses-of-the-virtual-machines-in-a-scale-set"></a>查詢擴展集中虛擬機器的公用 IP 位址
若要列出使用 CLI 指派給擴展集虛擬機器的公用 IP 位址，請使用 **az vmss list-instance-public-ips** 命令。

若要使用 PowerShell 列出擴展集公用 IP 位址，請使用 _Get-AzPublicIpAddress_ 命令。 例如:

```powershell
Get-AzPublicIpAddress -ResourceGroupName myrg -VirtualMachineScaleSetName myvmss
```

您也可以直接參考公用 IP 位址組態的資源識別碼，以查詢公用 IP 位址。 例如:

```powershell
Get-AzPublicIpAddress -ResourceGroupName myrg -Name myvmsspip
```

您也可以查詢 [Azure 資源總管](https://resources.azure.com)，或 Azure REST API 版本 **2017-03-30** 或更高版本，來顯示指派給擴展集虛擬機器的公用 IP 位址。

若要查詢 [Azure 資源總管](https://resources.azure.com)：

1. 在 Web 瀏覽器中開啟 [Azure 資源總管](https://resources.azure.com)。
1. 從左側按一下 [訂用帳戶]** 旁的 [+]**，即可展開訂用帳戶。 如果 [訂用帳戶]** 底下只有一個項目，則可能已經展開。
1. 展開您的訂用帳戶。
1. 展開您的資源群組。
1. 展開 [提供者]**。
1. 展開 [Microsoft.Compute]**。
1. 展開 [virtualMachineScaleSets]**。
1. 展開您的擴展集。
1. 按一下 [publicipaddresses]**。

若要查詢 Azure REST API：

```bash
GET https://management.azure.com/subscriptions/{your sub ID}/resourceGroups/{RG name}/providers/Microsoft.Compute/virtualMachineScaleSets/{scale set name}/publicipaddresses?api-version=2017-03-30
```

[Azure 資源總管](https://resources.azure.com)和 Azure REST API 的輸出範例：

```json
{
  "value": [
    {
      "name": "pub1",
      "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/pipvmss/virtualMachines/0/networkInterfaces/pipvmssnic/ipConfigurations/yourvmssipconfig/publicIPAddresses/pub1",
      "etag": "W/\"a64060d5-4dea-4379-a11d-b23cd49a3c8d\"",
      "properties": {
        "provisioningState": "Succeeded",
        "resourceGuid": "ee8cb20f-af8e-4cd6-892f-441ae2bf701f",
        "ipAddress": "13.84.190.11",
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Dynamic",
        "idleTimeoutInMinutes": 15,
        "ipConfiguration": {
          "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/0/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig"
        }
      }
    },
    {
      "name": "pub1",
      "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/3/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig/publicIPAddresses/pub1",
      "etag": "W/\"5f6ff30c-a24c-4818-883c-61ebd5f9eee8\"",
      "properties": {
        "provisioningState": "Succeeded",
        "resourceGuid": "036ce266-403f-41bd-8578-d446d7397c2f",
        "ipAddress": "13.84.159.176",
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Dynamic",
        "idleTimeoutInMinutes": 15,
        "ipConfiguration": {
          "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/3/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig"
        }
      }
    }
```

## <a name="multiple-ip-addresses-per-nic"></a>每個 NIC 的多個 IP 位址
擴展集中連接到 VM 的每個 NIC 皆有一或多個 IP 組態與其相關聯。 每個組態會獲派一個私人 IP 位址。 每個組態可能也會有一個關聯的公用 IP 位址資源。 若要了解多少個 IP 位址可以指派到 NIC，和您可以在 Azure 訂用帳戶中使用多少個公用 IP 位址，請參閱 [Azure 限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

## <a name="multiple-nics-per-virtual-machine"></a>每個虛擬機器的多個 NIC
每個虛擬機器可擁有最多 8 個 NIC，根據機器大小而定。 每部電腦的 NIC 最大數目可在[VM 大小文章](../virtual-machines/windows/sizes.md)中找到。 連線至 VM 執行個體的 NIC 全都必須連線至相同的虛擬網路。 NIC 可以連線至不同子網路，但子網路必須全都屬於相同的虛擬網路。

下列範例是顯示多個 NIC 項目的擴展集網路設定檔，以及每個虛擬機器的多個公用 IP：

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
        "name": "nic1",
        "properties": {
            "primary": true,
            "ipConfigurations": [
            {
                "name": "ip1",
                "properties": {
                "subnet": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                },
                "publicipaddressconfiguration": {
                    "name": "pub1",
                    "properties": {
                    "idleTimeoutInMinutes": 15
                    }
                },
                "loadBalancerInboundNatPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                    }
                ],
                "loadBalancerBackendAddressPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                    }
                ]
                }
            }
            ]
        }
        },
        {
        "name": "nic2",
        "properties": {
            "primary": false,
            "ipConfigurations": [
            {
                "name": "ip1",
                "properties": {
                "subnet": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                },
                "publicipaddressconfiguration": {
                    "name": "pub1",
                    "properties": {
                    "idleTimeoutInMinutes": 15
                    }
                },
                "loadBalancerInboundNatPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                    }
                ],
                "loadBalancerBackendAddressPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                    }
                ]
                }
            }
            ]
        }
        }
    ]
}
```

## <a name="nsg--asgs-per-scale-set"></a>每個擴展集的 NSG 和 ASG
[網路安全性群組](../virtual-network/security-overview.md)可讓您使用安全性規則篩選在 Azure 虛擬網路中進出於 Azure 資源的流量。 [應用程式安全性群組](../virtual-network/security-overview.md#application-security-groups)可讓您處理 Azure 資源的網路安全性，並將其群組為應用程式結構的擴充功能。

您可以將「網路安全性群組」直接套用至擴展集，方法是將參考新增至擴展集虛擬機器屬性的網路介面設定區段。

「應用程式安全性群組」也可以直接指定至擴展集，方法是將參考新增至擴展集虛擬機器屬性的網路介面 IP 組態區段。

例如:

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "nic1",
            "properties": {
                "primary": true,
                "ipConfigurations": [
                    {
                        "name": "ip1",
                        "properties": {
                            "subnet": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                            },
                            "applicationSecurityGroups": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/applicationSecurityGroups/', variables('asgName'))]"
                                }
                            ],
                "loadBalancerInboundNatPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                                }
                            ],
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                                }
                            ]
                        }
                    }
                ],
                "networkSecurityGroup": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('nsgName'))]"
                }
            }
        }
    ]
}
```

若要確認您的網路安全性群組是否與擴展集相關聯，請使用 `az vmss show` 命令。 下列範例會使用 `--query` 來篩選結果，並且只會顯示與輸出相關的區段。

```azurecli
az vmss show \
    -g myResourceGroup \
    -n myScaleSet \
    --query virtualMachineProfile.networkProfile.networkInterfaceConfigurations[].networkSecurityGroup

[
  {
    "id": "/subscriptions/.../resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/nsgName",
    "resourceGroup": "myResourceGroup"
  }
]
```

若要確認您的應用程式安全性群組是否與擴展集相關聯，請使用 `az vmss show` 命令。 下列範例會使用 `--query` 來篩選結果，並且只會顯示與輸出相關的區段。

```azurecli
az vmss show \
    -g myResourceGroup \
    -n myScaleSet \
    --query virtualMachineProfile.networkProfile.networkInterfaceConfigurations[].ipConfigurations[].applicationSecurityGroups

[
  [
    {
      "id": "/subscriptions/.../resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationSecurityGroups/asgName",
      "resourceGroup": "myResourceGroup"
    }
  ]
]
```



## <a name="next-steps"></a>後續步驟
如需 Azure 虛擬網路的詳細資訊，請參閱 [Azure 虛擬網路概觀](../virtual-network/virtual-networks-overview.md)。

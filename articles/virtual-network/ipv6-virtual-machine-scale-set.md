---
title: 在 Azure 中使用 IPv6 部署虛擬機器擴展集
titlesuffix: Azure Virtual Network
description: 本文說明如何在 Azure 虛擬網路中部署具有 IPv6 的虛擬機器擴展集。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: f969d7edc22c9c36481ca42449193af5f8c7b0d9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84709992"
---
# <a name="deploy-virtual-machine-scale-sets-with-ipv6-in-azure"></a>在 Azure 中使用 IPv6 部署虛擬機器擴展集

本文說明如何在 Azure 虛擬網路中部署具有雙重堆疊外部負載平衡器的雙重堆疊（IPv4 + IPv6）虛擬機器擴展集。 建立具備 IPv6 功能的虛擬機器擴展集的程式，與[此處](ipv6-configure-standard-load-balancer-template-json.md)所述建立個別 vm 的程式幾乎完全相同。 您將從類似于個別 Vm 所述的步驟著手：
1.    建立 IPv4 和 IPv6 公用 Ip。
2.    建立雙堆疊負載平衡器。  
3.    建立網路安全性群組（NSG）規則。  

不同于個別 Vm 的唯一步驟是建立使用虛擬機器擴展集資源的網路介面（NIC）設定： networkProfile/networkInterfaceConfigurations。 JSON 結構類似于個別 Vm 所使用的 networkInterfaces 物件，其中新增了 NIC 和 IPv4 IpConfiguration 作為主要介面，並使用 **"primary"： true**屬性，如下列範例所示：

```json
          "networkProfile": {
            "networkInterfaceConfigurations": [
              {
                "name": "[variables('nicName')]",
                "properties": {
                  "primary": true,
          "networkSecurityGroup": {
            "id": "[resourceId('Microsoft.Network/networkSecurityGroups','VmssNsg')]"
          },                  
                  "ipConfigurations": [
                    {
                      "name": "[variables('ipConfigName')]",
                      "properties": {
                        "primary": true,
                        "subnet": {
                          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', 'MyvirtualNetwork','Mysubnet')]"
                        },
                        "privateIPAddressVersion":"IPv4",                       
                        "publicipaddressconfiguration": {
                          "name": "pub1",
                          "properties": {
                            "idleTimeoutInMinutes": 15
                          }
                        },
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', 'loadBalancer', 'bePool'))]"
                          }
                        ],
                        "loadBalancerInboundNatPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/inboundNatPools', 'loadBalancer', 'natPool')]"
                          }
                        ]
                      }
                    },
                    {
                      "name": "[variables('ipConfigNameV6')]",
                      "properties": {
                        "subnet": {
                          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets','MyvirtualNetwork','Mysubnet')]"
                        },
                        "privateIPAddressVersion":"IPv6",
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', 'loadBalancer','bePoolv6')]"
                          }
                        ],                        
                      }
                    }
                  ]
                }
              }
            ]
          }

```


## <a name="sample-virtual-machine-scale-set-template-json"></a>範例虛擬機器擴展集範本 JSON

若要在[此](https://azure.microsoft.com/resources/templates/ipv6-in-vnet-vmss/)部署雙重堆疊（IPv4 + IPv6）虛擬機器擴展集與雙協定的外部 Load Balancer 和虛擬網路視圖範例範本。
## <a name="next-steps"></a>後續步驟

若要深入瞭解 Azure 虛擬網路中的 IPv6 支援，請參閱[什麼是適用于 azure 虛擬網路的 ipv6？](ipv6-overview.md)。

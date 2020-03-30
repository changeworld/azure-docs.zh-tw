---
title: 在 Azure 中部署使用 IPv6 的虛擬機器縮放集
titlesuffix: Azure Virtual Network
description: 本文演示如何在 Azure 虛擬網路中使用 IPv6 部署虛擬機器縮放集。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: kumud
ms.openlocfilehash: b90910614bcd86a54198b1a0961a3378427ea87e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73164987"
---
# <a name="deploy-virtual-machine-scale-sets-with-ipv6-in-azure-preview"></a>在 Azure 中部署使用 IPv6 的虛擬機器縮放集（預覽）

本文介紹如何在 Azure 虛擬網路中部署具有雙堆疊外部負載等化器的雙堆疊 （IPv4 + IPv6） 虛擬機器擴展集。 創建支援 IPv6 的虛擬機器規模集的過程幾乎與[創建此處](ipv6-configure-standard-load-balancer-template-json.md)描述的單個 VM 的過程相同。 您將從類似于各個 VM 描述的步驟開始：
1.  創建 IPv4 和 IPv6 公共 IP。
2.  創建雙堆疊負載等化器。  
3.  創建網路安全性群組 （NSG） 規則。  

與單個 VM 不同的唯一步驟是創建使用虛擬機器縮放集資源的網路介面 （NIC） 配置：網路設定檔/網路介面配置。 JSON 結構類似于用於單個 VM 的網路/網路介面物件，增加了使用 **"主"** 將 NIC 和 IPv4 Ip配置設置為主介面：真實屬性，如下例所示：

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


## <a name="sample-virtual-machine-scale-set-template-json"></a>示例虛擬機器縮放集範本 JSON

[在此處](https://azure.microsoft.com/resources/templates/ipv6-in-vnet-vmss/)部署具有雙堆疊外部負載等化器和虛擬網路視圖示例範本的雙堆疊 （IPv4 + IPv6） 虛擬機器擴展集。
## <a name="next-steps"></a>後續步驟

要瞭解有關 Azure 虛擬網路中 IPv6 支援的更多資訊，請參閱[什麼是 Azure 虛擬網路的 IPv6？](ipv6-overview.md)

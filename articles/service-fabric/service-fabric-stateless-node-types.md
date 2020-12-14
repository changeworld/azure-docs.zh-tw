---
title: 在 Service Fabric 叢集中部署無狀態的節點類型
description: 瞭解如何在 Azure Service fabric 叢集中建立和部署無狀態節點類型。
author: peterpogorski
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: pepogors
ms.openlocfilehash: d3ce6e888c937676027f2b71578c38b56f3bd6af
ms.sourcegitcommit: ea17e3a6219f0f01330cf7610e54f033a394b459
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2020
ms.locfileid: "97388015"
---
# <a name="deploy-an-azure-service-fabric-cluster-with-stateless-only-node-types-preview"></a>部署具有無狀態節點類型的 Azure Service Fabric 叢集 (Preview) 
Service Fabric 節點類型隨附固有的假設，在某個時間點，可設定狀態的服務可能會放在節點上。 無狀態節點類型可放寬此假設的節點類型，因此可讓節點類型使用其他功能（例如更快速的 scale out 作業）、支援以銅級持久性進行自動 OS 升級，以及在單一虛擬機器擴展集中相應放大至100個以上的節點。

* 主要節點類型無法設定為無狀態
* 只有銅級持久性層級支援無狀態節點類型
* 只有 Service Fabric 執行階段版本7.1.409 或更高版本才支援無狀態節點類型。


可用的範例範本： [Service Fabric 無狀態節點類型範本](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="enabling-stateless-node-types-in-service-fabric-cluster"></a>在 Service Fabric 叢集中啟用無狀態節點類型
若要在叢集資源中將一或多個節點類型設定為無狀態，請將 **isStateless** 屬性設定為 "true"。 部署具有無狀態節點類型的 Service Fabric 叢集時，請記得在叢集資源中至少要有一個主要節點類型。

```json
{
    "nodeTypes": [
    {
        "name": "[parameters('vmNodeType0Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt0applicationEndPort')]",
            "startPort": "[parameters('nt0applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt0fabricTcpGatewayPort')]",
        "durabilityLevel": "Bronze",
        "ephemeralPorts": {
            "endPort": "[parameters('nt0ephemeralEndPort')]",
            "startPort": "[parameters('nt0ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt0fabricHttpGatewayPort')]",
        "isPrimary": true,
        "isStateles": false,
        "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType1Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt1applicationEndPort')]",
            "startPort": "[parameters('nt1applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt1fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt1ephemeralEndPort')]",
            "startPort": "[parameters('nt1ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt1fabricHttpGatewayPort')]",
        "isPrimary": false,
        "isStateless": true,
        "vmInstanceCount": "[parameters('nt1InstanceCount')]"
    }    
    ],
}
```

## <a name="configuring-virtual-machine-scale-set-for-stateless-node-types"></a>設定無狀態節點類型的虛擬機器擴展集
若要啟用無狀態節點類型，您應以下列方式設定基礎虛擬機器擴展集資源：

* 值  **singlePlacementGroup** 屬性（property），視需要調整為 100 vm 的需求而定，應該設定為 true/false。
* 應設定為輪流的擴展集 **upgradeMode** 。
* 輪流升級模式需要設定應用程式健康情況延伸模組或健康情況探查。 設定健康情況探查與無狀態節點類型的預設設定，如下所建議。 一旦將應用程式部署至 nodetype 之後，就可以變更健康情況探查/健全狀況擴充埠來監視應用程式健康情況。

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "properties": {
        "overprovision": "[variables('overProvision')]",
        "upgradePolicy": {
          "mode": "Rolling",
          "automaticOSUpgradePolicy": {
            "enableAutomaticOSUpgrade": true
          }
        }
    }
    "virtualMachineProfile": {
    "extensionProfile": {
    "extensions": [
    {
    "name": "[concat(parameters('vmNodeType1Name'),'_ServiceFabricNode')]",
    "properties": {
        "type": "ServiceFabricNode",
        "autoUpgradeMinorVersion": false,
        "publisher": "Microsoft.Azure.ServiceFabric",
        "settings": {
            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
            "nodeTypeRef": "[parameters('vmNodeType1Name')]",
            "dataPath": "D:\\\\SvcFab",
            "durabilityLevel": "Silver",
            "certificate": {
                "thumbprint": "[parameters('certificateThumbprint')]",
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
            "systemLogUploadSettings": {
                "Enabled": true
            },
        },
        "typeHandlerVersion": "1.0"
    }
    },
    {
        "type": "extensions",
        "name": "HealthExtension",
        "properties": {
            "publisher": "Microsoft.ManagedServices",
            "type": "ApplicationHealthWindows",
            "autoUpgradeMinorVersion": true,
            "typeHandlerVersion": "1.0",
            "settings": {
            "protocol": "tcp",
            "port": "19000"
            }
            }
        },
    ]
}
```

## <a name="networking-requirements"></a>網路需求
### <a name="public-ip-and-load-balancer-resource"></a>公用 IP 和 Load Balancer 資源
若要在虛擬機器擴展集資源上啟用超過100個 Vm 的調整，該虛擬機器擴展集所參考的負載平衡器和 IP 資源必須同時使用 *標準* SKU。 建立負載平衡器或沒有 SKU 屬性的 IP 資源將會建立基本 SKU，而不支援調整至超過100的 Vm。 標準 SKU 負載平衡器預設會封鎖來自外部的所有流量;若要允許外部流量，必須將 NSG 部署到子網。

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat('LB','-', parameters('clusterName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
        "name": "Standard"
    }
}
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName')]", 
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', parameters('subnet0Name')))]"
    ],
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
        {
            "name": "[parameters('subnet0Name')]",
            "properties": {
                "addressPrefix": "[parameters('subnet0Prefix')]",
                "networkSecurityGroup": {
                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', parameters('subnet0Name')))]"
              }
            }
          }
        ]
    },
    "sku": {
        "name": "Standard"
    }
}
```

>[!NOTE]
> 您無法在公用 IP 和負載平衡器資源上進行 SKU 的就地變更。 如果您要從具有基本 SKU 的現有資源進行遷移，請參閱本文的「遷移」一節。

### <a name="virtual-machine-scale-set-nat-rules"></a>虛擬機器擴展集 NAT 規則
負載平衡器的輸入 NAT 規則應符合虛擬機器擴展集的 NAT 集區。 每個虛擬機器擴展集都必須有唯一的輸入 NAT 集區。

```json
{
"inboundNatPools": [
    {
        "name": "LoadBalancerBEAddressNatPool0",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "50999",
            "frontendPortRangeStart": "50000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool1",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "51999",
            "frontendPortRangeStart": "51000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool2",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "52999",
            "frontendPortRangeStart": "52000",
            "protocol": "tcp"
        }
    }
    ]
}
```

### <a name="standard-sku-load-balancer-outbound-rules"></a>標準 SKU Load Balancer 輸出規則
相較于使用基本 Sku，Standard Load Balancer 和標準公用 IP 引進了新功能和輸出連線能力的不同行為。 如果您想要在使用標準 SKU 時輸出連線，您必須使用標準公用 IP 位址或標準公用 Load Balancer 明確定義該連線。 如需詳細資訊，請參閱 [輸出](../load-balancer/load-balancer-outbound-connections.md) 連線和 [Azure Standard Load Balancer](../load-balancer/load-balancer-overview.md)。

>[!NOTE]
> 標準範本會參考 NSG，以根據預設允許所有輸出流量。 輸入流量僅限於 Service Fabric 管理作業所需的埠。 您可以修改 NSG 規則以符合您的需求。

>[!NOTE]
> 任何使用標準 SKU SLB 的 Service Fabric 叢集都必須確保每個節點類型都有一個規則，允許埠443上的輸出流量。 這是完成叢集設定的必要步驟，而且沒有這類規則的部署將會失敗。



### <a name="migrate-to-using-stateless-node-types-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>從使用基本 SKU Load Balancer 和基本 SKU IP 的叢集中，遷移至使用無狀態節點類型
若要遷移使用 Load Balancer 和 IP 與基本 SKU 的叢集，您必須先使用標準 SKU 建立全新的 Load Balancer 和 IP 資源。 不可能就地更新這些資源。

新的 LB 和 IP 應該在您要使用的新無狀態節點類型中參考。 在上述範例中，會新增新的虛擬機器擴展集資源，以用於無狀態的節點類型。 這些虛擬機器擴展集會參考新建立的 LB 和 IP，並在 Service Fabric 叢集資源中標示為無狀態節點類型。

若要開始，您必須將新的資源新增至現有的 Resource Manager 範本。 這些資源包括：
* 使用標準 SKU 的公用 IP 資源。
* 使用標準 SKU 的 Load Balancer 資源。
* 您要在其中部署虛擬機器擴展集的子網所參考的 NSG。


您可以在 [範例範本](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-2-NodeTypes-Windows-Stateless-Secure)中找到這些資源的範例。

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

資源完成部署之後，您就可以開始停用節點類型中想要從原始叢集移除的節點。

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 
```

## <a name="next-steps"></a>後續步驟 
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [節點類型和虛擬機器擴展集](service-fabric-cluster-nodetypes.md)


---
title: 在 Azure 中調整 Service Fabric 叢集
description: 在本教學課程中，您將了解如何在 Azure 中相應放大和縮小 Service Fabric 叢集，以及如何清除剩餘的資源。
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: 05fd29f874d59601942216f907010b94bb7c4058
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86257099"
---
# <a name="tutorial-scale-a-service-fabric-cluster-in-azure"></a>教學課程：在 Azure 中調整 Service Fabric 叢集

本教學課程為系列的第三個部分，解說如何相應放大和縮小現有叢集。 完成時，您將知道如何調整叢集，以及如何清除任何剩餘的資源。  如需如何調整在 Azure 中執行的叢集詳細資訊，請參閱[調整 Service Fabric 叢集](service-fabric-cluster-scaling.md)。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 新增和移除節點 (擴增和縮減)
> * 新增和移除節點類型 (擴增和縮減)
> * 增加節點資源 (擴大)

在本教學課程系列中，您將了解如何：
> [!div class="checklist"]
> * 在 Azure 上使用範本建立安全的 [Windows 叢集](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
> * [監視叢集](service-fabric-tutorial-monitor-cluster.md)
> * 將叢集相應縮小或相應放大
> * [升級叢集的執行階段](service-fabric-tutorial-upgrade-cluster.md)
> * [刪除叢集](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>必要條件

開始進行本教學課程之前：

* 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* 安裝 [Azure PowerShell](/powershell/azure/install-az-ps) 或 [Azure CLI](/cli/azure/install-azure-cli)。
* 在 Azure 上建立安全的 [Windows 叢集](service-fabric-tutorial-create-vnet-and-windows-cluster.md)

## <a name="important-considerations-and-guidelines"></a>重要考量和方針

應用程式工作負載會隨著時間而變更，您的現有服務是否需要更多 (或更少) 資源？  從節點類型[新增或移除節點](#add-nodes-to-or-remove-nodes-from-a-node-type)，以增加或減少叢集資源。

您需要在叢集中新增 100 個以上的節點嗎？  單一 Service Fabric 節點類型/擴展集不能包含 100 個以上的節點/VM。  若要將叢集調整為超過 100 個節點，請[新增其他節點類型](#add-nodes-to-or-remove-nodes-from-a-node-type)。

您的應用程式是否有多個服務，而且其中是否有任何服務必須是公開或網際網路對向的服務？  一般應用程式包含可接收用戶端輸入的前端閘道服務，以及一或多個與前端服務溝通的後端服務。 在此案例中，建議您在叢集中[至少新增兩個節點類型](#add-nodes-to-or-remove-nodes-from-a-node-type)。  

您的服務是否具有不同的基礎結構需求，例如，更多的 RAM 或更高的 CPU 週期？ 例如，您的應用程式包含一個前端服務和一個後端服務。 前端服務可以在容量較小 (D2 之類的 VM 大小)，且擁有可連線至網際網路之連接埠的 VM 上執行。 不過，需要大量計算的後端服務必須在容量較大 (D4、D6、D15 的 VM 大小)，且不連線至網際網路的 VM 上執行。 在此案例中，建議您在叢集中[新增兩個以上的節點類型](#add-nodes-to-or-remove-nodes-from-a-node-type)。 這可讓每個節點類型都有不同的屬性，例如，網際網路連線或 VM 大小。 VM 的數目也可以單獨調整。

在調整 Azure 叢集時，請記住下列指導方針︰

* 單一 Service Fabric 節點類型/擴展集不能包含 100 個以上的節點/VM。  若要將叢集調整為超過 100 個節點，請新增其他節點類型。
* 執行生產工作負載的主要節點類型應該具備金級或銀級的[持久性層級][durability]，而且一律具有五個以上的節點。
* 執行具狀態生產工作負載的非主要節點類型，應一律具有五個以上的節點。
* 執行無狀態生產工作負載的非主要節點類型，應一律具有兩個以上的節點。
* 任何具有金級或銀級[持久性層級][durability]的節點類型，應一律具有五個或更多節點。
* 如果將主要節點類型相應縮小 (從中移除節點)，您應該永遠不會將執行個體數目減少為低於[可靠性層級][reliability]所需的數目。

如需詳細資訊，請參閱[叢集容量指引](service-fabric-cluster-capacity.md)。

## <a name="export-the-template-for-the-resource-group"></a>匯出資源群組的範本

建立安全的 [Windows 叢集](service-fabric-tutorial-create-vnet-and-windows-cluster.md)且成功設定資源群組之後，請匯出資源群組的 Resource Manager 範本。 匯出範本，可讓您將叢集及其資源的未來部署自動化，因為範本包含所有完整的基礎結構。  如需匯出範本的詳細資訊，請參閱[使用 Azure 入口網站管理 Azure Resource Manager 資源群組](../azure-resource-manager/management/manage-resource-groups-portal.md)。

1. 在 [Azure 入口網站](https://portal.azure.com)，移至包含叢集的資源群組 (**sfclustertutorialgroup**，前提是您已遵循本教學課程)。 

2. 在左窗格中，選取 [部署]，或選取 [部署] 下方的連結。 

3. 從清單中選取最新的成功部署。

4. 在左窗格中，選取 [範本]，然後選取 [下載] 以將範本匯出為 ZIP 檔案。  將範本和參數儲存到您的本機電腦。

## <a name="add-nodes-to-or-remove-nodes-from-a-node-type"></a>從節點類型中新增或移除節點

相應縮小和放大，或水平調整時，請變更叢集中的節點數目。 當您縮減或擴增時，會將更多虛擬機器執行個體新增至擴展集。 這些執行個體會成為 Service Fabric 使用的節點。 擴展集有多個新增的執行個體 (透過相應放大) 時，Service Fabric 會知道並自動回應。 您可以隨時調整叢集，即使正在叢集上執行工作負載，也是如此。

### <a name="update-the-template"></a>更新範本

從最新部署的資源群組中[匯出範本和參數檔案](#export-the-template-for-the-resource-group)。  開啟 *parameters.json* 檔案。  如果您使用本教學課程中的[範例範本][template]來部署叢集，則叢集中會有三個叢集類型，以及三個可針對每個節點類型設定節點數目的參數：*nt0InstanceCount*、*nt1InstanceCount* 和 *nt2InstanceCount*。  *nt1InstanceCount* 參數，例如，設定第二個節點類型的執行個體計數，並在相關聯的虛擬機器擴展集中設定 VM 數目。

因此，您可以藉由更新 *nt1InstanceCount* 的值，來變更第二個節點類型中的節點數目。  請記住，您無法將節點類型相應放大為 100 個以上的節點。  執行具狀態生產工作負載的非主要節點類型，應一律具有五個以上的節點。 執行無狀態生產工作負載的非主要節點類型，應一律具有兩個以上的節點。

如果您要將銅級[持久性層級][durability]的節點類型相應縮小 (從中移除節點)，則必須[手動移除那些節點的狀態](service-fabric-cluster-scale-in-out.md#manually-remove-vms-from-a-node-typevirtual-machine-scale-set)。  如果是銀級和金級持久性層級，這些步驟就會由平台自動完成。

### <a name="deploy-the-updated-template"></a>部署更新的範本
將所有變更儲存到 *template.json* 和 *parameters.json* 檔案。  若要部署更新的範本，請執行下列命令：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ChangingInstanceCount"
```
或是下列 Azure CLI 命令：
```azurecli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="add-a-node-type-to-the-cluster"></a>將節點類型新增至叢集

在 Azure 內執行的 Service Fabric 叢集中定義的每個節點類型，都會安裝為[不同的虛擬機器擴展集](service-fabric-cluster-nodetypes.md)。 隨後，您即可個別管理每個節點類型。 您可以分開相應增加或減少每個節點類型，可以開啟不同組的連接埠，並使用不同的容量計量。 您也可以分別變更在每個叢集節點上執行的 OS SKU，但請注意，您不能在範例叢集中混合執行 Windows 和 Linux。 單一節點類型/擴展集所包含的節點不能超過 100 個。  您可以藉由新增其他節點類型/擴展集，來將叢集水平調整為 100 個以上的節點。 您可以隨時調整叢集，即使正在叢集上執行工作負載，也是如此。

### <a name="update-the-template"></a>更新範本

從最新部署的資源群組中[匯出範本和參數檔案](#export-the-template-for-the-resource-group)。  開啟 *parameters.json* 檔案。  如果您使用本教學課程中的[範例範本][template]來部署叢集，則叢集中會有三個節點類型。  在本節中，您可以藉由更新並部署 Resource Manager 範本，來新增第四個節點類型。 

除了新的節點類型，您也要新增相關聯的虛擬機器擴展集 (其會在虛擬網路的不同子網路中執行) 及網路安全性群組。  您可以選擇針對新的擴展集，新增新的或現有的公用 IP 位址和 Azure 負載平衡器資源。  新的節點類型具有銀級[持久性層級][durability]，且大小為 "Standard_D2_V2"。

在 *template.json* 檔案中，新增下列新的參數：
```json
"nt3InstanceCount": {
    "defaultValue": 5,
    "type": "Int",
    "metadata": {
        "description": "Instance count for node type"
    }
},
"vmNodeType3Size": {
    "defaultValue": "Standard_D2_V2",
    "type": "String"
},
```

在 *template.json* 檔案中，新增下列新的變數：
```json
"lbID3": "[resourceId('Microsoft.Network/loadBalancers',concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name')))]",
"lbIPConfig3": "[concat(variables('lbID3'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
"lbPoolID3": "[concat(variables('lbID3'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
"lbProbeID3": "[concat(variables('lbID3'),'/probes/FabricGatewayProbe')]",
"lbHttpProbeID3": "[concat(variables('lbID3'),'/probes/FabricHttpGatewayProbe')]",
"lbNatPoolID3": "[concat(variables('lbID3'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
"vmNodeType3Name": "[toLower(concat('NT4', variables('vmName')))]",
"vmStorageAccountName3": "[toLower(concat(uniqueString(resourceGroup().id), '1', '3' ))]",
"nt3applicationStartPort": "20000",
"nt3applicationEndPort": "30000",
"nt3ephemeralStartPort": "49152",
"nt3ephemeralEndPort": "65534",
"nt3fabricTcpGatewayPort": "19000",
"nt3fabricHttpGatewayPort": "19080",
"nt3reverseProxyEndpointPort": "19081",
"subnet3Name": "Subnet-3",
"subnet3Prefix": "10.0.3.0/24",
"subnet3Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet3Name'))]",
```

在 *template.json* 檔案中，將新的子網路新增至虛擬網路資源：
```json
{
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[variables('addressPrefix')]"
            ]
        },
        "subnets": [
            ...
            {
                "name": "[variables('subnet3Name')]",
                "properties": {
                    "addressPrefix": "[variables('subnet3Prefix')]",
                    "networkSecurityGroup": {
                        "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', variables('subnet3Name')))]"
                    }
                }
            }
        ]
    },
    "dependsOn": [
        ...
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', variables('subnet3Name')))]"
    ]
},
```

在 *template.json* 檔案中，新增新的公用 IP 位址和負載平衡器資源：
```json
{
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "dnsSettings": {
            "domainNameLabel": "[concat(variables('dnsName'),'-','nt4')]"
        },
        "publicIPAllocationMethod": "Dynamic"
    }
},
        {
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "frontendIPConfigurations": [
            {
                "name": "LoadBalancerIPConfig",
                "properties": {
                    "publicIPAddress": {
                        "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('lbIPName'),'-',variables('vmNodeType3Name')))]"
                    }
                }
            }
        ],
        "backendAddressPools": [
            {
                "name": "LoadBalancerBEAddressPool",
                "properties": {}
            }
        ],
        "loadBalancingRules": [
            {
                "name": "LBRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[variables('nt3fabricTcpGatewayPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[variables('nt3fabricTcpGatewayPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[variables('lbProbeID3')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "LBHttpRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[variables('nt3fabricHttpGatewayPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[variables('nt3fabricHttpGatewayPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[variables('lbHttpProbeID3')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortLBRule1",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[parameters('loadBalancedAppPort1')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[parameters('loadBalancedAppPort1')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID3'),'/probes/AppPortProbe1')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortLBRule2",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[parameters('loadBalancedAppPort2')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[parameters('loadBalancedAppPort2')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID3'),'/probes/AppPortProbe2')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            {
                "name": "FabricGatewayProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[variables('nt3fabricTcpGatewayPort')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "FabricHttpGatewayProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[variables('nt3fabricHttpGatewayPort')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortProbe1",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[parameters('loadBalancedAppPort1')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortProbe2",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[parameters('loadBalancedAppPort2')]",
                    "protocol": "tcp"
                }
            }
        ],
        "inboundNatPools": [
            {
                "name": "LoadBalancerBEAddressNatPool",
                "properties": {
                    "backendPort": "3389",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPortRangeEnd": "4500",
                    "frontendPortRangeStart": "3389",
                    "protocol": "tcp"
                }
            }
        ]
    },
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType3Name')))]"
    ]
},
```

在 *template.json* 檔案中，新增新的網路安全群組和虛擬機器擴展集資源。  虛擬機器擴展集的 Service Fabric 擴充功能屬性內的 NodeTypeRef 屬性，會將指定的節點類型對應至擴展集。

```json
{
    "type": "Microsoft.Network/networkSecurityGroups",
    "name": "[concat('nsg', variables('subnet3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[resourceGroup().location]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "securityRules": [
            {
                "name": "allowSvcFabSMB",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "445",
                    "direction": "Inbound",
                    "priority": 3950,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow SMB traffic within the net, used by fabric to move packages around"
                }
            },
            {
                "name": "allowSvcFabCluser",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "1025-1027",
                    "direction": "Inbound",
                    "priority": 3920,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow ports within vnet that are used by the fabric to talk between nodes"
                }
            },
            {
                "name": "allowSvcFabEphemeral",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[concat(variables('nt3ephemeralStartPort'), '-', variables('nt3ephemeralEndPort'))]",
                    "direction": "Inbound",
                    "priority": 3930,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow fabric ephemeral ports within the vnet"
                }
            },
            {
                "name": "allowSvcFabPortal",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3fabricHttpGatewayPort')]",
                    "direction": "Inbound",
                    "priority": 3900,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used to access the fabric cluster web portal"
                }
            },
            {
                "name": "allowSvcFabClient",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3fabricTcpGatewayPort')]",
                    "direction": "Inbound",
                    "priority": 3910,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used by the fabric client (includes powershell)"
                }
            },
            {
                "name": "allowSvcFabApplication",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[concat(variables('nt3applicationStartPort'), '-', variables('nt3applicationEndPort'))]",
                    "direction": "Inbound",
                    "priority": 3940,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow fabric application ports within the vnet"
                }
            },
            {
                "name": "blockAll",
                "properties": {
                    "access": "Deny",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "*",
                    "direction": "Inbound",
                    "priority": 4095,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "block all traffic except what we've explicitly allowed"
                }
            },
            {
                "name": "allowVNetRDP",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "3389",
                    "direction": "Inbound",
                    "priority": 3960,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow RDP within the net"
                }
            },
            {
                "name": "allowSvcFabReverseProxy",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3reverseProxyEndpointPort')]",
                    "direction": "Inbound",
                    "priority": 3980,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used to access the fabric cluster using reverse proxy"
                }
            },
            {
                "name": "allowAppPort1",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[parameters('loadBalancedAppPort1')]",
                    "direction": "Inbound",
                    "priority": 2001,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow public application port 1"
                }
            },
            {
                "name": "allowAppPort2",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[parameters('loadBalancedAppPort2')]",
                    "direction": "Inbound",
                    "priority": 2002,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow public application port 2"
                }
            }
        ]
    }
},
{
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "sku": {
        "name": "[parameters('vmNodeType3Size')]",
        "capacity": "[parameters('nt3InstanceCount')]",
        "tier": "Standard"
    },
    "name": "[variables('vmNodeType3Name')]",
    "apiVersion": "2018-10-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "overprovision": "[variables('overProvision')]",
        "upgradePolicy": {
            "mode": "Automatic"
        },
        "virtualMachineProfile": {
            "extensionProfile": {
                "extensions": [
                    {
                        "name": "[concat(variables('vmNodeType3Name'),'OMS')]",
                        "properties": {
                            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                            "type": "MicrosoftMonitoringAgent",
                            "typeHandlerVersion": "1.0",
                            "autoUpgradeMinorVersion": true,
                            "settings": {
                                "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
                            },
                            "protectedSettings": {
                                "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
                            }
                        }
                    },
                    {
                        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType3Name')]",
                        "properties": {
                            "type": "ServiceFabricNode",
                            "autoUpgradeMinorVersion": true,
                            "protectedSettings": {
                                "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                                "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                            },
                            "publisher": "Microsoft.Azure.ServiceFabric",
                            "settings": {
                                "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                                "nodeTypeRef": "[variables('vmNodeType3Name')]",
                                "dataPath": "D:\\SvcFab",
                                "durabilityLevel": "Silver",
                                "enableParallelJobs": true,
                                "nicPrefixOverride": "[variables('subnet3Prefix')]",
                                "certificate": {
                                    "thumbprint": "[parameters('certificateThumbprint')]",
                                    "x509StoreName": "[parameters('certificateStoreValue')]"
                                }
                            },
                            "typeHandlerVersion": "1.0"
                        }
                    },
                    {
                        "name": "[concat('VMDiagnosticsVmExt','_vmNodeType3Name')]",
                        "properties": {
                            "type": "IaaSDiagnostics",
                            "autoUpgradeMinorVersion": true,
                            "protectedSettings": {
                                "storageAccountName": "[variables('applicationDiagnosticsStorageAccountName')]",
                                "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                                "storageAccountEndPoint": "https://core.windows.net/"
                            },
                            "publisher": "Microsoft.Azure.Diagnostics",
                            "settings": {
                                "WadCfg": {
                                    "DiagnosticMonitorConfiguration": {
                                        "overallQuotaInMB": "50000",
                                        "EtwProviders": {
                                            "EtwEventSourceProviderConfiguration": [
                                                {
                                                    "provider": "Microsoft-ServiceFabric-Actors",
                                                    "scheduledTransferKeywordFilter": "1",
                                                    "scheduledTransferPeriod": "PT5M",
                                                    "DefaultEvents": {
                                                        "eventDestination": "ServiceFabricReliableActorEventTable"
                                                    }
                                                },
                                                {
                                                    "provider": "Microsoft-ServiceFabric-Services",
                                                    "scheduledTransferPeriod": "PT5M",
                                                    "DefaultEvents": {
                                                        "eventDestination": "ServiceFabricReliableServiceEventTable"
                                                    }
                                                }
                                            ],
                                            "EtwManifestProviderConfiguration": [
                                                {
                                                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                                                    "scheduledTransferLogLevelFilter": "Information",
                                                    "scheduledTransferKeywordFilter": "4611686018427387904",
                                                    "scheduledTransferPeriod": "PT5M",
                                                    "DefaultEvents": {
                                                        "eventDestination": "ServiceFabricSystemEventTable"
                                                    }
                                                }
                                            ]
                                        }
                                    }
                                },
                                "StorageAccount": "[variables('applicationDiagnosticsStorageAccountName')]"
                            },
                            "typeHandlerVersion": "1.5"
                        }
                    },
                    {
                        "name": "[concat('VMIaaSAntimalware','_vmNodeType3Name')]",
                        "properties": {
                            "publisher": "Microsoft.Azure.Security",
                            "type": "IaaSAntimalware",
                            "typeHandlerVersion": "1.5",
                            "settings": {
                                "AntimalwareEnabled": "true",
                                "Exclusions": {
                                    "Paths": "D:\\SvcFab;D:\\SvcFab\\Log;C:\\Program Files\\Microsoft Service Fabric",
                                    "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
                                },
                                "RealtimeProtectionEnabled": "true",
                                "ScheduledScanSettings": {
                                    "isEnabled": "true",
                                    "scanType": "Quick",
                                    "day": "7",
                                    "time": "120"
                                }
                            },
                            "protectedSettings": null
                        }
                    }
                ]
            },
            "networkProfile": {
                "networkInterfaceConfigurations": [
                    {
                        "name": "[concat(variables('nicName'), '-2')]",
                        "properties": {
                            "ipConfigurations": [
                                {
                                    "name": "[concat(variables('nicName'),'-',2)]",
                                    "properties": {
                                        "loadBalancerBackendAddressPools": [
                                            {
                                                "id": "[variables('lbPoolID3')]"
                                            }
                                        ],
                                        "loadBalancerInboundNatPools": [
                                            {
                                                "id": "[variables('lbNatPoolID3')]"
                                            }
                                        ],
                                        "subnet": {
                                            "id": "[variables('subnet3Ref')]"
                                        }
                                    }
                                }
                            ],
                            "primary": true
                        }
                    }
                ]
            },
            "osProfile": {
                "adminPassword": "[parameters('adminPassword')]",
                "adminUsername": "[parameters('adminUsername')]",
                "computernamePrefix": "[variables('vmNodeType3Name')]",
                "secrets": [
                    {
                        "sourceVault": {
                            "id": "[parameters('sourceVaultValue')]"
                        },
                        "vaultCertificates": [
                            {
                                "certificateStore": "[parameters('certificateStoreValue')]",
                                "certificateUrl": "[parameters('certificateUrlValue')]"
                            }
                        ]
                    }
                ]
            },
            "storageProfile": {
                "imageReference": {
                    "publisher": "[parameters('vmImagePublisher')]",
                    "offer": "[parameters('vmImageOffer')]",
                    "sku": "[parameters('vmImageSku')]",
                    "version": "[parameters('vmImageVersion')]"
                },
                "osDisk": {
                    "caching": "ReadOnly",
                    "createOption": "FromImage",
                    "managedDisk": {
                        "storageAccountType": "[parameters('storageAccountType')]"
                    }
                }
            }
        }
    },
    "dependsOn": [
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
        "[concat('Microsoft.Network/loadBalancers/', concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name')))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
    ]
},
```

在 *template.json* 檔案中，更新叢集資源並新增新的節點類型：
```json
{
    "type": "Microsoft.ServiceFabric/clusters",
    "name": "[parameters('clusterName')]",
    "apiVersion": "2018-02-01",
    "location": "[parameters('clusterLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "nodeTypes": [
            ...
            {
                "name": "[variables('vmNodeType3Name')]",
                "applicationPorts": {
                    "endPort": "[variables('nt3applicationEndPort')]",
                    "startPort": "[variables('nt3applicationStartPort')]"
                },
                "clientConnectionEndpointPort": "[variables('nt3fabricTcpGatewayPort')]",
                "durabilityLevel": "Silver",
                "ephemeralPorts": {
                    "endPort": "[variables('nt3ephemeralEndPort')]",
                    "startPort": "[variables('nt3ephemeralStartPort')]"
                },
                "httpGatewayEndpointPort": "[variables('nt3fabricHttpGatewayPort')]",
                "isPrimary": false,
                "reverseProxyEndpointPort": "[variables('nt3reverseProxyEndpointPort')]",
                "vmInstanceCount": "[parameters('nt3InstanceCount')]"
            }
        ],    
    }
}                
```

在 *parameters.json* 檔案中，新增下列新的參數和值：
```json
"nt3InstanceCount": {
    "Value": 5    
},
"vmNodeType3Size": {
    "Value": "Standard_D2_V2"
},
```

### <a name="deploy-the-updated-template"></a>部署更新的範本
將所有變更儲存到 *template.json* 和 *parameters.json* 檔案。  若要部署更新的範本，請執行下列命令：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "AddingNodeType"
```
或是下列 Azure CLI 命令：
```azurecli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="remove-a-node-type-from-the-cluster"></a>從叢集移除節點類型
建立 Service Fabric 叢集之後，您可以透過移除節點類型 (虛擬機器擴展集) 與其所有節點，來水平調整叢集規模。 您可以隨時調整叢集，即使正在叢集上執行工作負載，也是如此。 在叢集進行調整時，您的應用程式也會自動調整。

> [!WARNING]
> 不建議經常使用 Remove-AzServiceFabricNodeType 從生產環境叢集移除節點類型。 它是非常危險的命令，因為它會刪除節點類型後的虛擬機器擴展集資源。 

若要移除節點類型，請執行 [Remove-AzServiceFabricNodeType](/powershell/module/az.servicefabric/remove-azservicefabricnodetype) Cmdlet。  節點類型必須是銀級或金級[持久性層級][durability]。此 Cmdlet 會刪除與節點類型相關聯的擴展集，而且需要一些時間才能完成。  接著在每個要移除的節點上執行 [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) Cmdlet，以刪除節點狀態，並從叢集中移除節點。 如果節點上有服務，則會先將服務移出至另一個節點。 如果叢集管理員找不到複本/服務的節點，則作業會延遲/封鎖。

```powershell
$groupname = "sfclustertutorialgroup"
$nodetype = "nt4vm"
$clustername = "mysfcluster123"

Remove-AzServiceFabricNodeType -Name $clustername  -NodeType $nodetype -ResourceGroupName $groupname

Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.eastus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <thumbprint> `
          -FindType FindByThumbprint -FindValue <thumbprint> `
          -StoreLocation CurrentUser -StoreName My

$nodes = Get-ServiceFabricNode | Where-Object {$_.NodeType -eq $nodetype} | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending

Foreach($node in $nodes)
{
    Remove-ServiceFabricNodeState -NodeName $node.NodeName -TimeoutSec 300 -Force 
}
```

## <a name="increase-node-resources"></a>增加節點資源 
建立 Service Fabric 叢集之後，您可以使用新的節點類型 (包括更新的 VM SKU 或作業系統映像) 取代原始的節點類型，垂直調整叢集節點類型 (變更節點的資源) 或將節點類型 VM 的作業系統升級。 如需詳細的資訊，請參閱[擴大 Azure Service Fabric 節點類型](service-fabric-scale-up-node-type.md)。

> [!IMPORTANT]
> 請勿嘗試就地變更 VM SKU 或作業系統映像，這是一項危險的作業且不受支援。

如果此做法不可行，您可以從舊叢集建立新的叢集並[還原應用程式狀態](service-fabric-reliable-services-backup-restore.md) (如果適用的話)。 您不需要還原任何系統服務狀態，這些狀態會在您部署應用程式到新叢集時重新建立。 如果您之前只在叢集上執行無狀態應用程式，那麼您要做的只有部署應用程式到新叢集，不需還原任何東西。

### <a name="update-the-template"></a>更新範本

從最新部署的資源群組中[匯出範本和參數檔案](#export-the-template-for-the-resource-group)。  開啟 *parameters.json* 檔案。  如果您使用本教學課程中的[範例範本][template]來部署叢集，則叢集中會有三個節點類型。  

第二個節點類型中的 VM 大小設定於 *vmNodeType1Size* 參數中。  將 *vmNodeType1Size* 參數值從 Standard_D2_V2 變更為 [Standard_D3_V2](../virtual-machines/dv2-dsv2-series.md)，這讓每個 VM 執行個體的資源都能加倍。

適用於所有三個節點類型的 VM SKU 設定於 *vmImageSku* 參數中。  同樣地，應該謹慎處理變更節點類型的 VM SKU，不建議針對主要節點類型進行變更。

### <a name="deploy-the-updated-template"></a>部署更新的範本
將所有變更儲存到 *template.json* 和 *parameters.json* 檔案。  若要部署更新的範本，請執行下列命令：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ScaleUpNodeType"
```
或是下列 Azure CLI 命令：
```azurecli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 新增和移除節點 (擴增和縮減)
> * 新增和移除節點類型 (擴增和縮減)
> * 增加節點資源 (擴大)

接下來，請前往下列教學課程，了解如何升級叢集執行階段。
> [!div class="nextstepaction"]
> [升級叢集的執行階段](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster
[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json

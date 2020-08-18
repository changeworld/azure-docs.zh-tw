---
title: 設定服務端點原則-Azure HDInsight
description: 瞭解如何使用 Azure HDInsight 為您的虛擬網路設定服務端點原則。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 07/15/2020
ms.openlocfilehash: f2752e5ab2bf7c2926ec9e2c0e4929eab91ed377
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88530965"
---
# <a name="configure-virtual-network-service-endpoint-policies-for-azure-hdinsight"></a>設定 Azure HDInsight 的虛擬網路服務端點原則

本文提供如何在具有 Azure HDInsight 的虛擬網路上執行服務端點原則的相關資訊。

## <a name="background"></a>背景

Azure HDInsight 可讓您在自己的虛擬網路中建立叢集。 如果您需要允許從虛擬網路連至其他 Azure 服務（例如儲存體帳戶）的連出流量，您可以建立 [服務端點原則](../virtual-network/virtual-network-service-endpoint-policies-overview.md)。 但是，透過 Azure 入口網站所建立的服務端點原則，只允許您為單一帳戶、訂用帳戶中的所有帳戶，或是資源群組中的所有帳戶建立原則。

不過，Azure HDInsight 為受控服務，會從每個區域的特定儲存體帳戶中的每個叢集收集資料和記錄檔。 為了讓此資料從您的虛擬網路連線至 HDInsight，您必須建立服務端點原則，以允許連至受 Azure HDInsight 管理之特定資料收集點的連出流量。

## <a name="service-endpoint-policies-for-hdinsight"></a>適用于 HDInsight 的服務端點原則

這些服務端點原則支援下列功能：

- 叢集建立、作業執行和平臺作業（例如調整）的記錄和遙測收集。
- 將虛擬硬碟) 連接到新建立的叢集節點，以在叢集上布建軟體和程式庫， (Vhd。

如果未建立服務端點原則來啟用此資料流程，叢集建立可能會失敗，且 Azure HDInsight 將無法為您的叢集提供支援。

## <a name="create-service-endpoint-policies-for-hdinsight"></a>建立 HDInsight 的服務端點原則

建立新的叢集之前，請確定已將正確的服務端點原則附加至您的虛擬網路。 否則，叢集建立可能會失敗或導致錯誤。

使用下列程式來建立必要的服務端點原則：

1. 決定您將建立 HDInsight 叢集的區域。
1. 在 [服務端點原則資源清單](https://github.com/Azure-Samples/hdinsight-enterprise-security/blob/main/hdinsight-service-endpoint-policy-resources.json)中查閱該區域，以提供 HDInsight 管理儲存體帳戶的所有資源群組。
1. 選取您區域的資源群組清單。 的資源範例如下 `Canada Central` 所示：

    ```json
    "Canada Central":[
        "/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",
        "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",
        "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG"
    ],
    ```

1. 將該資源群組清單插入以 Azure CLI 或 Azure PowerShell 撰寫的設定腳本中。

    ```azurecli
    $subscriptionId = "<subscription id>"
    $rgName="<resource group name> "
    $location="<location name>"
    $vnetName="<vnet name>"
    $subnetName="<subnet name>"
    $sepName="<service endpoint policy name>"
    $sepDefName="<service endpoint policy definition name>"
    
    # Set to the right subscription ID
    az account set --subscription $subscriptionId
    
    # setup service endpoint on the virtual network subnet
    az network vnet subnet update -g $rgName --vnet-name $vnetName -n $subnetName --service-endpoints Microsoft.Storage
    
    # Create Service Endpoint Policy
    az network service-endpoint policy create -g $rgName  -n $sepName -l $location
    
    # Insert the list of HDInsight owned resources for the region your clusters will be created in.
    [String[]]$resources = @("/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",`
    "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",`
    "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG")
    
    #Assign service resources to the SEP policy.
    az network service-endpoint policy-definition create -g $rgName --policy-name $sepName -n $sepDefName --service "Microsoft.Storage" --service-resources $resources
    
    # Associate a subnet to the service endpoint policy just created. If there is a delay in updating it to subnet, you can use the Azure portal to associate the policy with the subnet.
    az network vnet subnet update -g $rgName --vnet-name $vnetName -n $subnetName --service-endpoint-policy $sepName
    ```

    如果您想要使用 PowerShell 來設定服務端點原則，請使用下列程式碼片段。
    
    ```powershell
    #Script to assign SEP 
    $subscriptionId = "<subscription id>"
    $rgName = "<resource group name>"
    $vnetName = "<vnet name>"
    $subnetName = "<subnet Name"
    $location = "Canada Central"
    
    # Connect to your Azure Account
    Connect-AzAccount
    
    # Select the Subscription that you want to use
    Select-AzSubscription -SubscriptionId $subscriptionId
    
    # Retrieve VNet Config
    $vnet = Get-AzVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
    
    # Retrieve Subnet Config
    $subnet = Get-AzVirtualNetworkSubnetConfig -Name $subnetName -VirtualNetwork $vnet
    
    # Insert the list of HDInsight owned resources for the region your clusters will be created in.
    [String[]]$resources = @("/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",
    "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",
    "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG")
    
    #Declare service endpoint policy definition
    $sepDef = New-AzServiceEndpointPolicyDefinition -Name "SEPHDICanadaCentral" -Description "Service Endpoint Policy Definition" -Service "Microsoft.Storage" -ServiceResource $resources
    
    # Service Endpoint Policy
    $sep= New-AzServiceEndpointPolicy -ResourceGroupName $rgName -Name "SEPHDICanadaCentral" -Location $location -ServiceEndpointPolicyDefinition $sepDef
    
    # Associate a subnet to the service endpoint policy just created. If there is a delay in updating it to subnet, you can use the Azure portal to associate the policy with the subnet.
    Set-AzVirtualNetworkSubnetConfig -Name $subnetName -VirtualNetwork $vnet -AddressPrefix $subnet.AddressPrefix -ServiceEndpointPolicy $sep
    ```

## <a name="next-steps"></a>後續步驟

* [Azure HDInsight 虛擬網路架構](hdinsight-virtual-network-architecture.md)
* [設定網路虛擬設備](./network-virtual-appliance.md)

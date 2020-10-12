---
title: 使用 Azure Red Hat OpenShift 3.11 建立私人叢集 | Microsoft Docs
description: 使用 Azure Red Hat OpenShift 3.11 建立私人叢集
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/02/2020
keywords: aro, openshift, private cluster, red hat, 私人叢集
ms.openlocfilehash: f4ce6c79fa9fe6d05fdea4b877a8aa7faf404a9b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "83727446"
---
# <a name="create-a-private-cluster-with-azure-red-hat-openshift-311"></a>使用 Azure Red Hat OpenShift 3.11 建立私人叢集

私人叢集提供下列優點：

* 私人叢集不會在公用 IP 位址上公開叢集控制平面元件 (例如 API 伺服器)。
* 私人叢集的虛擬網路可由客戶設定，可讓您設定網路功能，以允許與其他虛擬網路 (包括 ExpressRoute 環境) 進行對等互連。 您也可以在虛擬網路上設定自訂 DNS，以便與內部服務整合。

## <a name="before-you-begin"></a>開始之前

下列設定程式碼片段中的欄位是新的，而且必須包含在您的叢集設定中。 `managementSubnetCidr` 必須位於叢集虛擬網路內，且由 Azure 用於管理叢集。

```json
properties:
 networkProfile:
   managementSubnetCidr: 10.0.1.0/24
 masterPoolProfile:
   apiProperties:
     privateApiServer: true
```

您可以使用下面提供的範例指令碼來部署私人叢集。 部署叢集之後，請執行 `cluster get` 命令，並檢視 `properties.FQDN` 屬性，以判斷 OpenShift API 伺服器的私人 IP 位址。

叢集虛擬網路將會以權限建立，讓您可以加以修改。 接著，您可以視需要設定網路來存取虛擬網路 (ExpressRoute、VPN、虛擬網路對等互連)。

如果您變更叢集虛擬網路上的 DNS 名稱伺服器，則必須在叢集上發出更新，並將 `properties.RefreshCluster` 屬性設定為 `true`，才能為 VM 重新安裝映像。 此更新可讓其挑選新的名稱伺服器。

## <a name="sample-configuration-scripts"></a>範例設定指令碼

使用此節中的範例指令碼來設定及部署您的私人叢集。

### <a name="environment"></a>環境

使用您自己的值填入下面的環境變數。

> [!NOTE]
> 位置必須設定為 `eastus2`，因為這是目前針對私人叢集唯一支援的位置。

``` bash
export CLUSTER_NAME=
export LOCATION=eastus2
export TOKEN=$(az account get-access-token --query 'accessToken' -o tsv)
export SUBID=
export TENANT_ID=
export ADMIN_GROUP=
export CLIENT_ID=
export SECRET=
```

### <a name="private-clusterjson"></a>private-cluster.json

以下是已啟用私人叢集的範例叢集設定，其使用上面定義的環境變數。

```json
{
   "location": "$LOCATION",
   "name": "$CLUSTER_NAME",
   "properties": {
       "openShiftVersion": "v3.11",
       "networkProfile": {
           "vnetCIDR": "10.0.0.0/8",
           "managementSubnetCIDR" : "10.0.1.0/24"
       },
       "authProfile": {
           "identityProviders": [
               {
                   "name": "Azure AD",
                   "provider": {
                       "kind": "AADIdentityProvider",
                       "clientId": "$CLIENT_ID",
                       "secret": "$SECRET",
                       "tenantId": "$TENANT_ID",
                       "customerAdminGroupID": "$ADMIN_GROUP"
                   }
               }
           ]
       },
       "masterPoolProfile": {
           "name": "master",
           "count": 3,
           "vmSize": "Standard_D4s_v3",
           "osType": "Linux",
           "subnetCIDR": "10.0.0.0/24",
           "apiProperties": {
                "privateApiServer": true
            }
       },
       "agentPoolProfiles": [
           {
               "role": "compute",
               "name": "compute",
               "count": 1,
               "vmSize": "Standard_D4s_v3",
               "osType": "Linux",
               "subnetCIDR": "10.0.0.0/24"
           },
           {
               "role": "infra",
               "name": "infra",
               "count": 3,
               "vmSize": "Standard_D4s_v3",
               "osType": "Linux",
               "subnetCIDR": "10.0.0.0/24"
           }
       ],
       "routerProfiles": [
           {
               "name": "default"
           }
       ]
   }
}
```

## <a name="deploy-a-private-cluster"></a>部署私人叢集

使用上述範例指令碼設定您的私人叢集之後，請執行下列命令來部署私人叢集。

``` bash
az group create --name $CLUSTER_NAME --location $LOCATION
cat private-cluster.json | envsubst | curl -v -X PUT \
-H 'Content-Type: application/json; charset=utf-8' \
-H 'Authorization: Bearer '$TOKEN'' -d @- \
 https://management.azure.com/subscriptions/$SUBID/resourceGroups/$CLUSTER_NAME/providers/Microsoft.ContainerService/openShiftManagedClusters/$CLUSTER_NAME?api-version=2019-10-27-preview
```

## <a name="next-steps"></a>後續步驟

若要了解如何存取 OpenShift 主控台，請參閱 [Web 主控台逐步解說](https://docs.openshift.com/container-platform/3.11/getting_started/developers_console.html) \(英文\)。

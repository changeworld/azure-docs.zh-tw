---
title: 使用 Azure 紅帽開放Shift 3.11 創建專用群集 |微軟文檔
description: 使用 Azure 紅帽開放Shift 3.11 創建專用群集
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/02/2020
keywords: aro， 開放式移位， 私人集群， 紅帽子
ms.openlocfilehash: b34b5d622527742447847102526eba9ee6ca220d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399415"
---
# <a name="create-a-private-cluster-with-azure-red-hat-openshift-311"></a>使用 Azure 紅帽開放Shift 3.11 創建專用群集

> [!IMPORTANT]
> Azure 紅帽 OpenShift （ARO） 專用群集目前僅在美國東部 2 的專用預覽版中可用。 專用預覽接受僅通過邀請。 在嘗試啟用此功能之前，請確保註冊訂閱。

專用群集提供以下好處：

* 專用群集不會在公共 IP 位址上公開群集控制平面元件（如 API 伺服器）。
* 專用群集的虛擬網路可由客戶配置，允許您設置網路以允許與其他虛擬網路（包括 ExpressRoute 環境）對等互連。 您還可以在虛擬網路上配置自訂 DNS 以與內部服務集成。

## <a name="before-you-begin"></a>開始之前

> [!NOTE]
> 此功能需要 ARO HTTP API 的版本 2019-10-27 預覽。 Azure CLI 中尚不支援它。

以下配置程式碼片段中的欄位是新的，必須包含在群集配置中。 `managementSubnetCidr`必須在群集虛擬網路中，並且 Azure 使用 它來管理群集。

```json
properties:
 networkProfile:
   managementSubnetCidr: 10.0.1.0/24
 masterPoolProfile:
   apiProperties:
     privateApiServer: true
```

可以使用下面提供的示例腳本部署專用群集。 部署群集後，`cluster get`執行命令並查看`properties.FQDN`屬性以確定 OpenShift API 伺服器的私人 IP 位址。

群集虛擬網路將具有許可權創建，以便您可以對其進行修改。 然後，您可以根據需要設置網路以訪問虛擬網路（ExpressRoute、VPN、虛擬網路對等互連）。

如果更改群集虛擬網路上的 DNS 名稱伺服器，則需要在群集上發出更新，`properties.RefreshCluster`並將屬性設置為，`true`以便可以重新映射 VM。 此更新將允許他們拿起新的名稱伺服器。

## <a name="sample-configuration-scripts"></a>示例配置腳本

使用本節中的示例腳本設置和部署專用群集。

### <a name="environment"></a>環境

將下面的環境變數填報為使用您自己的值。

> [!NOTE]
> 必須將位置設置為`eastus2`，因為當前這是專用群集的唯一受支援的位置。

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

### <a name="private-clusterjson"></a>私有群集.json

使用上面定義的環境變數，下面是啟用專用群集的示例群集配置。

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

## <a name="deploy-a-private-cluster"></a>部署專用群集

使用上面的示例腳本配置專用群集後，運行以下命令以部署專用群集。

``` bash
az group create --name $CLUSTER_NAME --location $LOCATION
cat private-cluster.json | envsubst | curl -v -X PUT \
-H 'Content-Type: application/json; charset=utf-8' \
-H 'Authorization: Bearer '$TOKEN'' -d @- \
 https://management.azure.com/subscriptions/$SUBID/resourceGroups/$CLUSTER_NAME/providers/Microsoft.ContainerService/openShiftManagedClusters/$CLUSTER_NAME?api-version=2019-10-27-preview
```

## <a name="next-steps"></a>後續步驟

要瞭解如何訪問 OpenShift 主控台，請參閱[Web 主控台演練](https://docs.openshift.com/container-platform/3.11/getting_started/developers_console.html)。

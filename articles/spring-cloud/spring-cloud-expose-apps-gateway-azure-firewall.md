---
title: 使用應用程式閘道和 Azure 防火牆將應用程式公開至網際網路
description: 如何使用應用程式閘道和 Azure 防火牆將應用程式公開至網際網路
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/17/2020
ms.custom: devx-track-java
ms.openlocfilehash: d5bd62dad5be7f6a6df5b6b037e8eeae13ee48e3
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98887170"
---
# <a name="expose-applications-to-the-internet-using-application-gateway-and-azure-firewall"></a>使用應用程式閘道和 Azure 防火牆將應用程式公開至網際網路

本檔說明如何使用應用程式閘道和 Azure 防火牆將應用程式公開至網際網路。 當您的虛擬網路中部署 Azure 春季雲端服務實例時，服務實例上的應用程式只能在私人網路中存取。 若要讓應用程式可在網際網路上存取，您必須與 **Azure 應用程式閘道** 整合，並選擇性地與 **Azure 防火牆** 整合。

## <a name="prerequisites"></a>先決條件

- [Azure CLI 版本2.0.4 版或更新版本](https://docs.microsoft.com/cli/azure/install-azure-cli)。

## <a name="define-variables"></a>定義變數

針對您建立的資源群組和虛擬網路定義變數，以在 [azure 虛擬網路中部署 Azure 春天雲端 (VNet 插入) ](spring-cloud-tutorial-deploy-in-azure-virtual-network.md)。 根據您的實際環境自訂值。

```
SUBSCRIPTION='subscription-id'
RESOURCE_GROUP='my-resource-group'
LOCATION='eastus'
SPRING_APP_PRIVATE_FQDN='my-azure-spring-cloud-hello-vnet.private.azuremicroservices.io'
VIRTUAL_NETWORK_NAME='azure-spring-cloud-vnet'
APPLICATION_GATEWAY_SUBNET_NAME='app-gw-subnet'
APPLICATION_GATEWAY_SUBNET_CIDR='10.1.2.0/24'
```

## <a name="login-to-azure"></a>登入 Azure

登入 Azure CLI 並選擇您的有效定用帳戶。

```
az login
az account set --subscription ${SUBSCRIPTION}
```

## <a name="create-network-resources"></a>建立網路資源

要建立的 **Azure 應用程式閘道** 會將與--或對等互連虛擬網路相同的虛擬網路加入至 Azure 春季雲端服務實例。 首先，使用為虛擬網路中的應用程式閘道建立新的子網 `az network vnet subnet create` ，並使用建立公用 IP 位址作為應用程式閘道的前端 `az network public-ip create` 。

```
APPLICATION_GATEWAY_PUBLIC_IP_NAME='app-gw-public-ip'
az network vnet subnet create \
    --name ${APPLICATION_GATEWAY_SUBNET_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --vnet-name ${VIRTUAL_NETWORK_NAME} \
    --address-prefix ${APPLICATION_GATEWAY_SUBNET_CIDR}
az network public-ip create \
    --resource-group ${RESOURCE_GROUP} \
    --location ${LOCATION} \
    --name ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --allocation-method Static \
    --sku Standard
```

## <a name="create-application-gateway"></a>建立應用程式閘道

使用建立應用程式閘道 `az network application-gateway create` ，並指定應用程式的私用完整功能變數名稱 (FQDN) 作為後端集區中的伺服器。 然後使用將 HTTP 設定更新 `az network application-gateway http-settings update` 為使用後端集區的主機名稱。

```
APPLICATION_GATEWAY_NAME='my-app-gw'
az network application-gateway create \
    --name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --location ${LOCATION} \
    --capacity 2 \
    --sku Standard_v2 \
    --http-settings-cookie-based-affinity Enabled \
    --http-settings-port 443 \
    --http-settings-protocol Https \
    --public-ip-address ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --vnet-name ${VIRTUAL_NETWORK_NAME} \
    --subnet ${APPLICATION_GATEWAY_SUBNET_NAME} \
    --servers ${SPRING_APP_PRIVATE_FQDN}
az network application-gateway http-settings update \
    --gateway-name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --name appGatewayBackendHttpSettings \
    --host-name-from-backend-pool true
```

Azure 建立應用程式閘道最多可能需要 30 分鐘。 建立之後，請使用來檢查後端健全狀況 `az network application-gateway show-backend-health` 。  這會檢查應用程式閘道是否透過其私人 FQDN 抵達您的應用程式。

```
az network application-gateway show-backend-health \
    --name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP}
```

輸出會指出後端集區的狀況良好狀態。

```
{
  "backendAddressPools": [
    {
      "backendHttpSettingsCollection": [
        {
          "servers": [
            {
              "address": "my-azure-spring-cloud-hello-vnet.private.azuremicroservices.io",
              "health": "Healthy",
              "healthProbeLog": "Success. Received 200 status code",
              "ipConfiguration": null
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="access-your-application-using-the-frontend-public-ip-of-the-application-gateway"></a>使用應用程式閘道的前端公用 IP 存取您的應用程式

使用來取得應用程式閘道的公用 IP 位址 `az network public-ip show` 。

```
az network public-ip show \
    --resource-group ${RESOURCE_GROUP} \
    --name ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --query [ipAddress] \
    --output tsv
```

將公用 IP 位址複製並貼到瀏覽器的網址列中。

  ![公用 IP 中的應用程式](media/spring-cloud-expose-apps-gateway-az-firewall/app-gateway-public-ip.png)

## <a name="see-also"></a>另請參閱

- [針對 VNET 中的 Azure Spring Cloud 進行疑難排解](spring-cloud-troubleshooting-vnet.md)
- [客戶在 VNET 中執行 Azure Spring Cloud 的責任](spring-cloud-vnet-customer-responsibilities.md)
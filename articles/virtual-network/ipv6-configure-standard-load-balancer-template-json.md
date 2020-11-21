---
title: 在 Azure 虛擬網路中部署 IPv6 雙重堆疊應用程式-Resource manager 範本
titlesuffix: Azure Virtual Network
description: 本文說明如何使用 Azure Resource Manager VM 範本，在 Azure 虛擬網路中部署具有 Standard Load Balancer 的 IPv6 雙重堆疊應用程式。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 732c3305b9d678bedc29e490153fac650398a238
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95016168"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure-virtual-network---template"></a>在 Azure 虛擬網路中部署 IPv6 雙重堆疊應用程式-範本

本文提供 IPv6 設定工作的清單，其中包含適用于 Azure Resource Manager VM 範本的部分。 使用本文中所述的範本，在 Azure 中使用 Standard Load Balancer 部署雙重堆疊 (IPv4 + IPv6) 應用程式，其中包含具有 IPv4 和 IPv6 子網的雙重堆疊虛擬網路、雙 (IPv4 + IPv6) 前端設定的 Standard Load Balancer、具有雙重 IP 設定、網路安全性群組和公用 Ip 的 Nic。 

## <a name="required-configurations"></a>必要設定

搜尋範本中的範本區段，以查看應該發生的位置。

### <a name="ipv6-addressspace-for-the-virtual-network"></a>虛擬網路的 IPv6 addressSpace

要加入的範本區段：

```JSON
        "addressSpace": {
          "addressPrefixes": [
            "[variables('vnetv4AddressRange')]",
            "[variables('vnetv6AddressRange')]"    
```

### <a name="ipv6-subnet-within-the-ipv6-virtual-network-addressspace"></a>IPv6 虛擬網路內的 IPv6 子網 addressSpace

要加入的範本區段：
```JSON
          {
            "name": "V6Subnet",
            "properties": {
              "addressPrefix": "[variables('subnetv6AddressRange')]"
            }

```

### <a name="ipv6-configuration-for-the-nic"></a>NIC 的 IPv6 設定

要加入的範本區段：
```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

### <a name="ipv6-network-security-group-nsg-rules"></a>IPv6 網路安全性群組 (NSG) 規則

```JSON
          {
            "name": "default-allow-rdp",
            "properties": {
              "description": "Allow RDP",
              "protocol": "Tcp",
              "sourcePortRange": "33819-33829",
              "destinationPortRange": "5000-6000",
              "sourceAddressPrefix": "2001:db8:deca:deed::/64",
              "destinationAddressPrefix": "2001:db8:deca:deed::/64",
              "access": "Allow",
              "priority": 1003,
              "direction": "Inbound"
            }
```

## <a name="conditional-configuration"></a>條件式設定

如果您使用的是網路虛擬裝置，請在路由表中新增 IPv6 路由。 否則，這項設定是選擇性的。

```JSON
    {
      "type": "Microsoft.Network/routeTables",
      "name": "v6route",
      "apiVersion": "[variables('ApiVersion')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "routes": [
          {
            "name": "v6route",
            "properties": {
              "addressPrefix": "2001:db8:deca:deed::/64",
              "nextHopType": "VirtualAppliance",
              "nextHopIpAddress": "2001:db8:ace:f00d::1"
            }
```

## <a name="optional-configuration"></a>選用設定

### <a name="ipv6-internet-access-for-the-virtual-network"></a>虛擬網路的 IPv6 網際網路存取

```JSON
{
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-public-ip-addresses"></a>IPv6 公用 IP 位址

```JSON
    {
      "apiVersion": "[variables('ApiVersion')]",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "lbpublicip-v6",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "properties": {
        "publicIPAllocationMethod": "Static",
        "publicIPAddressVersion": "IPv6"
      }
```

### <a name="ipv6-front-end-for-load-balancer"></a>Load Balancer 的 IPv6 前端

```JSON
          {
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-back-end-address-pool-for-load-balancer"></a>Load Balancer 的 IPv6 後端位址集區

```JSON
              "backendAddressPool": {
                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', 'loadBalancer'), '/backendAddressPools/LBBAP-v6')]"
              },
              "protocol": "Tcp",
              "frontendPort": 8080,
              "backendPort": 8080
            },
            "name": "lbrule-v6"
```

### <a name="ipv6-load-balancer-rules-to-associate-incoming-and-outgoing-ports"></a>IPv6 負載平衡器規則，用來建立傳入和傳出埠的關聯

```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

## <a name="sample-vm-template-json"></a>範例 VM 範本 JSON
若要使用 Azure Resource Manager 範本，在 Azure 虛擬網路中部署 IPv6 雙重堆疊應用程式，請參閱 [這裡](https://azure.microsoft.com/resources/templates/ipv6-in-vnet-stdlb/)的範例範本。

## <a name="next-steps"></a>下一步

您可以找到[公用 IP 位址](https://azure.microsoft.com/pricing/details/ip-addresses/)、[網路頻寬](https://azure.microsoft.com/pricing/details/bandwidth/)或 [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/) 的定價詳細資料。

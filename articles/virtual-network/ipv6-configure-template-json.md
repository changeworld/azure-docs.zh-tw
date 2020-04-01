---
title: 在 Azure 虛擬網路中使用基本負載均衡器部署 IPv6 雙堆疊應用程式 - 資源管理器樣本
titlesuffix: Azure Virtual Network
description: 本文演示如何使用 Azure 資源管理器 VM 範本在 Azure 虛擬網路中部署 IPv6 雙堆疊應用程式。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 7c2f66d92597801aa7c6b0b32f86f594d3effb05
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420608"
---
# <a name="deploy-an-ipv6-dual-stack-application-with-basic-load-balancer-in-azure---template"></a>在 Azure - 樣本中部署具有基本負載均衡器的 IPv6 雙堆疊應用程式

本文提供了 IPv6 配置任務的清單,其中介紹了適用於 Azure 資源管理器 VM 範本的部分。 使用本文中描述的範本,使用基本負載均衡器部署雙堆疊 (IPv4 + IPv6) 應用程式,該應用程式包括包含 IPv4 和 IPv6 子網的雙堆疊虛擬網路、具有雙 (IPv4 + IPv6) 前端配置的基本負載均衡器、具有雙 IP 配置的 NIC 的 VM、網路安全組和公共 IP。

要使用標準負載均衡器部署雙堆疊 (IPV4+ IPv6) 應用程式,請參閱[使用標準負載均衡器部署 IPv6 雙堆疊應用程式 - 樣本](ipv6-configure-standard-load-balancer-template-json.md)。

## <a name="required-configurations"></a>所需設定

在範本中搜索範本部分以查看它們應發生的位置。

### <a name="ipv6-addressspace-for-the-virtual-network"></a>虛擬網路的 IPv6 位址空間

要新增的樣本部份:

```JSON
        "addressSpace": {
          "addressPrefixes": [
            "[variables('vnetv4AddressRange')]",
            "[variables('vnetv6AddressRange')]"    
```

### <a name="ipv6-subnet-within-the-ipv6-virtual-network-addressspace"></a>IPv6 虛擬網路位址空間中的 IPv6 子網路

要新增的樣本部份:
```JSON
          {
            "name": "V6Subnet",
            "properties": {
              "addressPrefix": "[variables('subnetv6AddressRange')]"
            }

```

### <a name="ipv6-configuration-for-the-nic"></a>NIC 的 IPv6 設定

要新增的樣本部份:
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

### <a name="ipv6-network-security-group-nsg-rules"></a>IPv6 網路安全群組 (NSG) 規則

```JSON
          {
            "name": "default-allow-rdp",
            "properties": {
              "description": "Allow RDP",
              "protocol": "Tcp",
              "sourcePortRange": "33819-33829",
              "destinationPortRange": "5000-6000",
              "sourceAddressPrefix": "ace:cab:deca:deed::/64",
              "destinationAddressPrefix": "cab:ace:deca:deed::/64",
              "access": "Allow",
              "priority": 1003,
              "direction": "Inbound"
            }
```

## <a name="conditional-configuration"></a>條件設定

如果您使用的是網路虛擬設備,請在路由表中添加 IPv6 路由。 否則,此配置是可選的。

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
              "addressPrefix": "ace:cab:deca:deed::/64",
              "nextHopType": "VirtualAppliance",
              "nextHopIpAddress": "deca:cab:ace:f00d::1"
            }
```

## <a name="optional-configuration"></a>選用設定

### <a name="ipv6-internet-access-for-the-virtual-network"></a>虛擬網路的 IPv6 網際網路

```JSON
{
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-public-ip-addresses"></a>IPv6 公共 IP 位址

```JSON
    {
      "apiVersion": "[variables('ApiVersion')]",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "lbpublicip-v6",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "publicIPAddressVersion": "IPv6"
      }
```

### <a name="ipv6-front-end-for-load-balancer"></a>用於負載均衡器的 IPv6 前端

```JSON
          {
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-back-end-address-pool-for-load-balancer"></a>用於負載均衡器的 IPv6 後端位址池

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

### <a name="ipv6-load-balancer-rules-to-associate-incoming-and-outgoing-ports"></a>IPv6 負載均衡器規則用於關聯傳入和傳出埠

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

## <a name="sample-vm-template-json"></a>範例 VM 樣本 JSON
要使用 Azure 資源管理器樣本在 Azure 虛擬網路中部署具有基本負載均衡器的 IPv6 雙堆疊應用程式,[請在此處](https://azure.microsoft.com/resources/templates/ipv6-in-vnet/)查看範例樣本。

## <a name="next-steps"></a>後續步驟

您可以找到有關公共 IP[位址](https://azure.microsoft.com/pricing/details/ip-addresses/)、[網路頻寬](https://azure.microsoft.com/pricing/details/bandwidth/)或[負載均衡器](https://azure.microsoft.com/pricing/details/load-balancer/)定價的詳細資訊。

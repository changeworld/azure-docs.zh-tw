---
title: 在 Azure 虛擬網路中部署 IPv6 雙堆疊應用程式 - 資源管理器範本（預覽）
titlesuffix: Azure Virtual Network
description: 本文演示如何使用 Azure 資源管理器 VM 範本在 Azure 虛擬網路中使用標準負載等化器部署 IPv6 雙堆疊應用程式。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 07/15/2019
ms.author: kumud
ms.openlocfilehash: 8f2c6bc7fb7ab0939da20932fd531c158549ce7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "70012857"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure-virtual-network---template-preview"></a>在 Azure 虛擬網路中部署 IPv6 雙堆疊應用程式 - 範本（預覽）

本文提供了 IPv6 配置任務的清單，其仲介紹了適用于 Azure 資源管理器 VM 範本的部分。 使用本文中描述的範本，使用 Azure 中的標準負載等化器部署雙堆疊 （IPv4 + IPv6） 應用程式，該應用程式包括包含 IPv4 和 IPv6 子網的雙堆疊虛擬網路，該網路是具有雙 （IPv4 + IPv6） 前端的標準負載等化器配置、具有具有雙 IP 配置的 NIC 的 VM、網路安全性群組和公共 IP。 

## <a name="required-configurations"></a>所需配置

在範本中搜索範本部分以查看它們應發生的位置。

### <a name="ipv6-addressspace-for-the-virtual-network"></a>虛擬網路的 IPv6 位址空間

要添加的範本部分：

```JSON
        "addressSpace": {
          "addressPrefixes": [
            "[variables('vnetv4AddressRange')]",
            "[variables('vnetv6AddressRange')]"    
```

### <a name="ipv6-subnet-within-the-ipv6-virtual-network-addressspace"></a>IPv6 虛擬網路位址空間中的 IPv6 子網

要添加的範本部分：
```JSON
          {
            "name": "V6Subnet",
            "properties": {
              "addressPrefix": "[variables('subnetv6AddressRange')]"
            }

```

### <a name="ipv6-configuration-for-the-nic"></a>NIC 的 IPv6 配置

要添加的範本部分：
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

### <a name="ipv6-network-security-group-nsg-rules"></a>IPv6 網路安全性群組 （NSG） 規則

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

## <a name="conditional-configuration"></a>條件配置

如果您使用的是網路虛擬裝置，請在路由表中添加 IPv6 路由。 否則，此配置是可選的。

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

### <a name="ipv6-internet-access-for-the-virtual-network"></a>虛擬網路的 IPv6 互聯網接入

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
      "sku": {
        "name": "Standard"
      },
      "properties": {
        "publicIPAllocationMethod": "Static",
        "publicIPAddressVersion": "IPv6"
      }
```

### <a name="ipv6-front-end-for-load-balancer"></a>用於負載等化器的 IPv6 前端

```JSON
          {
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-back-end-address-pool-for-load-balancer"></a>用於負載等化器的 IPv6 後端位址集區

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

### <a name="ipv6-load-balancer-rules-to-associate-incoming-and-outgoing-ports"></a>IPv6 負載等化器規則用於關聯傳入和傳出埠

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

## <a name="sample-vm-template-json"></a>示例 VM 範本 JSON
要使用 Azure 資源管理器範本在 Azure 虛擬網路中部署 IPv6 雙堆疊應用程式，[請在此處](https://azure.microsoft.com/resources/templates/ipv6-in-vnet-stdlb/)查看示例範本。

## <a name="next-steps"></a>後續步驟

您可以找到有關公共 IP[位址](https://azure.microsoft.com/pricing/details/ip-addresses/)、[網路頻寬](https://azure.microsoft.com/pricing/details/bandwidth/)或[負載等化器](https://azure.microsoft.com/pricing/details/load-balancer/)定價的詳細資訊。

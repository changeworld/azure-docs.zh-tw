---
title: Terraform：部署以 VNet 整合和私人端點安全連線的前端 Web 應用程式和後端 Web 應用程式
description: 了解如何使用 App Service 的 Terraform 提供者來部署兩個以私人端點和 VNet 整合安全連線的 Web 應用程式
author: ericgre
ms.assetid: 3e5d1bbd-5581-40cc-8f65-bc74f1802156
ms.topic: sample
ms.date: 08/10/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: c1de8ebbd9ad381628cfeb19413baa295b42b3db
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91739828"
---
# <a name="create-two-web-apps-connected-securely-with-private-endpoint-and-vnet-integration"></a>建立兩個以私人端點和 VNet 整合安全連線的 Web 應用程式

本文以範例說明如何使用下列步驟，透過[私人端點](../networking/private-endpoint.md)和區域 [VNet 整合](../web-sites-integrate-with-vnet.md)來安全地連結兩個 Web 應用程式 (前端和後端)：
- 部署 VNet
- 建立用於整合的第一個子網路
- 建立用於私人端點的第二個子網路，您必須設定特定參數來停用網路原則
- 部署一個 PremiumV2 或 PremiumV3 類型的 App Service 方案，私人端點功能需要這樣的類型
- 建立具有特定應用程式設定的前端 Web 應用程式，以取用私人 DNS 區域，[更多詳細資料](../web-sites-integrate-with-vnet.md#azure-dns-private-zones)
- 將前端 Web 應用程式連線到整合子網路
- 建立後端 Web 應用程式
- 使用 Web 應用程式的私人連結區域名稱 privatelink.azurewebsites.net 建立 DNS 私人區域
- 將此區域連結至 VNet
- 在端點子網路中建立後端 Web 應用程式的私人端點，並在先前建立的 DNS 私人區域中註冊 DNS 名稱 (網站和 SCM)

## <a name="how-to-use-terraform-in-azure"></a>如何在 Azure 中使用 Terraform

瀏覽至 [Azure 文件](/azure/developer/terraform/)，以了解如何搭配使用 Terraform 與 Azure。

## <a name="the-complete-terraform-file"></a>完整的 Terraform 檔案

若要使用此檔案，您必須變更 frontwebapp 和 backwebapp 資源的 name 屬性 (webapp 名稱必須是全球唯一的 DNS 名稱)。 

```hcl
provider "azurerm" {
  version = "~>2.0"
  features {}
}

resource "azurerm_resource_group" "rg" {
  name     = "appservice-rg"
  location = "francecentral"
}

resource "azurerm_virtual_network" "vnet" {
  name                = "vnet"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  address_space       = ["10.0.0.0/16"]
}

resource "azurerm_subnet" "integrationsubnet" {
  name                 = "integrationsubnet"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = ["10.0.1.0/24"]
  delegation {
    name = "delegation"
    service_delegation {
      name = "Microsoft.Web/serverFarms"
    }
  }
}

resource "azurerm_subnet" "endpointsubnet" {
  name                 = "endpointsubnet"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = ["10.0.2.0/24"]
  enforce_private_link_endpoint_network_policies = true
}

resource "azurerm_app_service_plan" "appserviceplan" {
  name                = "appserviceplan"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name

  sku {
    tier = "Premiumv2"
    size = "P1v2"
  }
}

resource "azurerm_app_service" "frontwebapp" {
  name                = "frontwebapp20200810"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  app_service_plan_id = azurerm_app_service_plan.appserviceplan.id

  app_settings = {
    "WEBSITE_DNS_SERVER": "168.63.129.16",
    "WEBSITE_VNET_ROUTE_ALL": "1"
  }
}

resource "azurerm_app_service_virtual_network_swift_connection" "vnetintegrationconnection" {
  app_service_id  = azurerm_app_service.frontwebapp.id
  subnet_id       = azurerm_subnet.integrationsubnet.id
}

resource "azurerm_app_service" "backwebapp" {
  name                = "backwebapp20200810"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  app_service_plan_id = azurerm_app_service_plan.appserviceplan.id
}

resource "azurerm_private_dns_zone" "dnsprivatezone" {
  name                = "privatelink.azurewebsites.net"
  resource_group_name = azurerm_resource_group.rg.name
}

resource "azurerm_private_dns_zone_virtual_network_link" "dnszonelink" {
  name = "dnszonelink"
  resource_group_name = azurerm_resource_group.rg.name
  private_dns_zone_name = azurerm_private_dns_zone.dnsprivatezone.name
  virtual_network_id = azurerm_virtual_network.vnet.id
}

resource "azurerm_private_endpoint" "privateendpoint" {
  name                = "backwebappprivateendpoint"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  subnet_id           = azurerm_subnet.endpointsubnet.id

  private_dns_zone_group {
    name = "privatednszonegroup"
    private_dns_zone_ids = [azurerm_private_dns_zone.dnsprivatezone.id]
  }

  private_service_connection {
    name = "privateendpointconnection"
    private_connection_resource_id = azurerm_app_service.backwebapp.id
    subresource_names = ["sites"]
    is_manual_connection = false
  }
}
```




## <a name="next-steps"></a>後續步驟


> [深入了解如何使用 Azure 中的 Terraform](/azure/developer/terraform/)
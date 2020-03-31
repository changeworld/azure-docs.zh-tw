---
title: 為 ISI 設置公共出站 IP 位址
description: 瞭解如何在 Azure 邏輯應用中為整合服務環境 （ISEs） 設置單個公共出站 IP 位址
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 619c68b84291bc35b8216194ac4534393fde454c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191542"
---
# <a name="set-up-a-single-ip-address-for-one-or-more-integration-service-environments-in-azure-logic-apps"></a>為 Azure 邏輯應用中的一個或多個整合服務環境設置單個 IP 位址

使用 Azure 邏輯應用時，可以設置[*整合服務環境*（ISE），](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)以託管需要訪問[Azure 虛擬網路中](../virtual-network/virtual-networks-overview.md)的資源的邏輯應用。 如果有多個 ISE 實例需要訪問具有 IP 限制的其他終結點，請將[Azure 防火牆](../firewall/overview.md)或[網路虛擬裝置](../virtual-network/virtual-networks-overview.md#filter-network-traffic)部署到虛擬網路，並通過該防火牆或網路虛擬裝置路由出站流量。 然後，您可以讓虛擬網路中的所有 ISE 實例使用單個、公共、靜態和可預測的 IP 位址與目標系統通信。 這樣，您就不必為每個 ISE 在這些目標系統設置額外的防火牆打開。

本主題演示如何通過 Azure 防火牆路由出站流量，但可以將類似概念應用於網路虛擬裝置（如 Azure 應用商店中的協力廠商防火牆）。 雖然本主題側重于多個 ISE 實例的設置，但當方案需要限制需要訪問的 IP 位址數時，也可以對單個 ISE 使用此方法。 考慮防火牆或虛擬網路設備的額外成本是否適合您的方案。 瞭解有關[Azure 防火牆定價](https://azure.microsoft.com/pricing/details/azure-firewall/)的資訊。

## <a name="prerequisites"></a>Prerequisites

* 與 ISE 在同一虛擬網路中運行的 Azure 防火牆。 如果沒有防火牆，請先添加已命名的`AzureFirewallSubnet`[子網](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet)到虛擬網路。 然後，您可以在虛擬網路中[創建和部署防火牆](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall)。

* Azure[路由表](../virtual-network/manage-route-table.md)。 如果沒有路由表，請先[創建一個路由表](../virtual-network/manage-route-table.md#create-a-route-table)。 有關路由的詳細資訊，請參閱[虛擬網路流量路由](../virtual-network/virtual-networks-udr-overview.md)。

## <a name="set-up-route-table"></a>設置路由表

1. 在[Azure 門戶](https://portal.azure.com)中，選擇路由表，例如：

   ![選擇具有定向出站流量規則的路由表](./media/connect-virtual-network-vnet-set-up-single-ip-address/select-route-table-for-virtual-network.png)

1. 要在路由表功能表上[添加新路由](../virtual-network/manage-route-table.md#create-a-route)，請選擇 **"路由** > **添加**"。

   ![添加用於定向出站流量的路由](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-route-to-route-table.png)

1. 在 **"添加路由"** 窗格中，使用規則[設置新路由](../virtual-network/manage-route-table.md#create-a-route)，該規則指定到目標系統的所有傳出流量都遵循此行為：

   * 使用[**虛擬裝置**](../virtual-network/virtual-networks-udr-overview.md#user-defined)作為下一個躍點類型。

   * 轉到防火牆實例的私人 IP 位址作為下一個躍點位址。

     要查找此 IP 位址，請在防火牆功能表上選擇 **"概述**"，查找**私人 IP 位址**下的位址，例如：

     ![查找防火牆私人 IP 位址](./media/connect-virtual-network-vnet-set-up-single-ip-address/find-firewall-private-ip-address.png)

   下面是一個示例，該示例顯示了此類規則的外觀：

   ![設置定向出站流量的規則](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-rule-to-route-table.png)

   | 屬性 | 值 | 描述 |
   |----------|-------|-------------|
   | **路由名稱** | <*唯一路由名稱*> | 路由表中路由的唯一名稱 |
   | **位址首碼** | <*目標位址*> | 目標系統的位址，您希望流量轉到的位置。 請確保為此位址使用[無類域間路由 （CIDR） 標記法](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)。 |
   | **下一個躍點類型** | **虛擬設備** | 出站流量使用的[躍點類型](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools) |
   | **下一個躍點位址** | <*防火牆-私人 IP 位址*> | 防火牆的私人 IP 位址 |
   |||

## <a name="set-up-network-rule"></a>設置網路規則

1. 在 Azure 門戶中，查找並選擇防火牆。 在防火牆功能表上，**在"設置"** 下，選擇 **"規則**"。 在規則窗格中，選擇**網路規則集合** > **"添加網路規則集合**"。

   ![將網路規則集合添加到防火牆](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-network-rule-collection.png)

1. 在集合中，添加允許流量到目標系統的規則。

   例如，假設您有一個在 ISE 中運行的邏輯應用，並且需要與 SFTP 系統通信。 創建名為`LogicApp_ISE_SFTP_Outbound`的網路規則集合，其中包含名為 的網路`ISE_SFTP_Outbound`規則。 此規則允許使用防火牆的私人 IP 位址從 ISE 在虛擬網路中運行的任何子網的 IP 位址到目標 SFTP 系統進行流量。

   ![為防火牆設置網路規則](./media/connect-virtual-network-vnet-set-up-single-ip-address/set-up-network-rule-for-firewall.png)

   **網路規則集合屬性**

   | 屬性 | 值 | 描述 |
   |----------|-------|-------------|
   | **名稱** | <*網路規則集合名稱*> | 網路規則集合的名稱 |
   | **優先順序** | <*優先順序*> | 用於運行規則集合的優先順序順序。 有關詳細資訊，請參閱什麼是[Azure 防火牆概念](../firewall/firewall-faq.md#what-are-some-azure-firewall-concepts)？ |
   | **動作** | **允許** | 要為此規則執行的操作類型 |
   |||

   **網路規則屬性**

   | 屬性 | 值 | 描述 |
   |----------|-------|-------------|
   | **名稱** | <*網路規則名稱*> | 網路規則的名稱 |
   | **協定** | <*連線協定*> | 要使用的連線協定。 例如，如果您使用的是 NSG 規則，請選擇**TCP**和**UDP**，而不僅僅是**TCP**。 |
   | **源位址** | <*ISE 子網位址*> | ISE 運行的子網 IP 位址以及邏輯應用的流量來源 |
   | **目標位址** | <*目標 IP 位址*> | 您希望流量所在的目標系統的 IP 位址 |
   | **目的地連接埠** | <*目的地埠*> | 目標系統用於入站通信的任何埠 |
   |||

   有關網路規則的詳細資訊，請參閱以下文章：

   * [設定網路規則](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)
   * [Azure 防火牆規則處理邏輯](../firewall/rule-processing.md#network-rules-and-applications-rules)
   * [Azure 防火牆常見問題集](../firewall/firewall-faq.md)
   * [Azure 電源外殼：新-A 防火牆網路規則](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule)
   * [Azure CLI：az 網路防火牆網路規則](https://docs.microsoft.com/cli/azure/ext/azure-firewall/network/firewall/network-rule?view=azure-cli-latest#ext-azure-firewall-az-network-firewall-network-rule-create)

## <a name="next-steps"></a>後續步驟

* [從 Azure Logic Apps 連線到 Azure 虛擬網路](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
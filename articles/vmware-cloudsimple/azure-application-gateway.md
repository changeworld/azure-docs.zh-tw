---
title: 將 Azure 應用程式閘道與 VMware 虛擬機器一起使用
description: 描述如何使用 Azure 應用程式閘道管理 VMware 虛擬機器中運行的 Web 服務器的傳入 Web 流量，以贏得雲簡單私有雲環境
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2cbfdd358fdfd5403c677c067376142169cdc6bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77015451"
---
# <a name="use-azure-application-gateway-with-vmware-virtual-machines-in-the-cloudsimple-private-cloud-environment"></a>在雲簡單私有雲環境中將 Azure 應用程式閘道與 VMware 虛擬機器一起使用

您可以使用 Azure 應用程式閘道管理雲簡單私有雲環境中 VMware 虛擬機器中運行的 Web 服務器傳入 Web 流量。

通過在公私混合部署中利用 Azure 應用程式閘道，可以管理應用程式的 Web 流量、提供安全前端和卸載 SSL 處理，以便其服務在 VMware 環境中運行。 Azure 應用程式閘道根據配置的規則和運行狀況探測將傳入 Web 流量路由到駐留在 VMware 環境中的後端池實例。

此 Azure 應用程式閘道解決方案要求您：

* 擁有 Azure 訂用帳戶。
* 創建和配置 Azure 虛擬網路和虛擬網路中的子網。
* 創建和配置 NSG 規則，並使用 ExpressRoute 與雲簡單私有雲對等 vNet。
* 創建&配置私有雲。
* 創建&配置 Azure 應用程式閘道。

## <a name="azure-application-gateway-deployment-scenario"></a>Azure 應用程式閘道部署方案

在這種情況下，Azure 應用程式閘道在 Azure 虛擬網路中運行。 虛擬網路通過 ExpressRoute 電路連接到您的私有雲。 私有雲中的所有子網都是從虛擬網路子網可達到 IP 的。

![Azure 虛擬網路中的 Azure 負載等化器](media/load-balancer-use-case.png)

## <a name="how-to-deploy-the-solution"></a>如何部署解決方案

部署過程由以下任務組成：

1. [驗證是否滿足先決條件](#1-verify-prerequisites)
2. [將 Azure 虛擬連接連接到私有雲](#2-connect-your-azure-virtual-network-to-your-private-cloud)
3. [部署 Azure 應用程式閘道](#3-deploy-an-azure-application-gateway)
4. [在私有雲中創建和配置 Web 服務器 VM 池](#4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud)

## <a name="1-verify-prerequisites"></a>1. 驗證先決條件

驗證是否滿足這些先決條件：

* 已創建 Azure 資源管理器和虛擬網路。
* 已在 Azure 虛擬網路中創建專用子網（用於應用程式閘道）。
* 雲簡單私有雲已創建。
* 虛擬網路中的 IP 子網與私有雲中的子網之間沒有 IP 衝突。

## <a name="2-connect-your-azure-virtual-network-to-your-private-cloud"></a>2. 將 Azure 虛擬網路連接到私有雲

要將 Azure 虛擬網路連接到私有雲，請按照此過程操作。

1. [在雲簡單門戶中，複製 ExpressRoute 對等資訊](virtual-network-connection.md)。

2. [為 Azure 虛擬網路配置虛擬網路閘道](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)。

3. [將虛擬網路連結到雲簡單快速路由電路](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)。

4. [使用複製的對等資訊將虛擬網路連結到 ExpressRoute 電路](virtual-network-connection.md)。

## <a name="3-deploy-an-azure-application-gateway"></a>3. 部署 Azure 應用程式閘道

有關此的詳細資訊，可在使用 Azure[門戶使用基於路徑的路由規則創建應用程式閘道](../application-gateway/create-url-route-portal.md)中。 以下是所需步驟的摘要：

1. 在訂閱和資源組中創建虛擬網路。
2. 在虛擬網路中創建子網（用作專用子網）。
3. 創建標準應用程式閘道（可選地啟用 WAF）：從 Azure 門戶主頁，從頁面左上角按一下**資源** > **網路** > **應用程式閘道**。 選擇標準 SKU 和大小並提供 Azure 訂閱、資源組和位置資訊。 如果需要，請為此應用程式閘道創建新的公共 IP，並提供虛擬網路和應用程式閘道專用子網的詳細資訊。
4. 使用虛擬機器添加後端池並將其添加到應用程式閘道。

## <a name="4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud"></a>4. 在私有雲中創建和配置 Web 服務器 VM 池

在 vCenter 中，使用您選擇的作業系統和 Web 服務器（如 Windows/IIS 或 Linux/Apache）創建 VM。 選擇為私有雲中的 Web 層指定的子網/VLAN。 驗證 Web 服務器 VM 的至少一個 vNIC 是否位於 Web 層子網中。

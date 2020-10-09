---
title: 搭配使用 Azure 應用程式閘道與 VMware 虛擬機器
description: 說明如何使用 Azure 應用程式閘道來管理在 VMware 虛擬機器中執行之 web 伺服器的連入網路流量，並贏得 CloudSimple 的私用雲端環境。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b7dce0181987b7e61b243a7eb0e13b7ed687eb08
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "82185688"
---
# <a name="use-azure-application-gateway-with-vmware-virtual-machines-in-the-cloudsimple-private-cloud-environment"></a>使用 Azure 應用程式閘道搭配 CloudSimple 私人雲端環境中的 VMware 虛擬機器

您可以使用 Azure 應用程式閘道來管理 CloudSimple 私用雲端環境中 VMware 虛擬機器上執行之 web 伺服器的連入網路流量。

藉由在公用私人混合式部署中利用 Azure 應用程式閘道，您可以管理應用程式的網路流量、提供安全的前端，以及卸載其在 VMware 環境中執行之服務的 TLS 處理。 Azure 應用程式閘道會根據設定的規則和健康情況探查，將連入的網路流量路由傳送至位於 VMware 環境的後端集區實例。

此 Azure 應用程式閘道解決方案需要您：

* 擁有 Azure 訂用帳戶。
* 建立和設定 Azure 虛擬網路，以及在虛擬網路內建立子網。
* 使用 ExpressRoute 建立和設定 NSG 規則，並將您的 vNet 對等互連至 CloudSimple 私用雲端。
* 建立 & 設定您的私人雲端。
* 建立 & 設定 Azure 應用程式閘道。

## <a name="azure-application-gateway-deployment-scenario"></a>Azure 應用程式閘道部署案例

在此案例中，Azure 應用程式閘道會在您的 Azure 虛擬網路中執行。 虛擬網路透過 ExpressRoute 線路連接到您的私人雲端。 私人雲端中的所有子網都可從虛擬網路子網存取 IP。

![Azure 虛擬網路中的 azure 負載平衡器](media/load-balancer-use-case.png)

## <a name="how-to-deploy-the-solution"></a>如何部署解決方案

部署套裝程式含下列工作：

1. [確認符合必要條件](#1-verify-prerequisites)
2. [將您的 Azure 虛擬連線連線到私人雲端](#2-connect-your-azure-virtual-network-to-your-private-cloud)
3. [部署 Azure 應用程式閘道](#3-deploy-an-azure-application-gateway)
4. [在您的私人雲端中建立及設定 Web 服務器 VM 集區](#4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud)

## <a name="1-verify-prerequisites"></a>1. 確認必要條件

確認符合下列必要條件：

* 已建立 Azure Resource Manager 和虛擬網路。
* 已在您的 Azure 虛擬網路內建立應用程式閘道) 的專用子網 (。
* 已建立 CloudSimple 私人雲端。
* 虛擬網路中的 IP 子網和私人雲端中的子網之間沒有 IP 衝突。

## <a name="2-connect-your-azure-virtual-network-to-your-private-cloud"></a>2. 將 Azure 虛擬網路連線到您的私人雲端

若要將您的 Azure 虛擬網路連線到您的私人雲端，請依照此程式操作。

1. [在 CloudSimple 入口網站中，複製 ExpressRoute 對等互連資訊](virtual-network-connection.md)。

2. [設定 Azure 虛擬網路的虛擬網路閘道](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)。

3. 將[您的虛擬網路連結至 CloudSimple ExpressRoute 線路](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)。

4. [使用您所複製的對等互連資訊，將虛擬網路連結至 ExpressRoute 線路](virtual-network-connection.md)。

## <a name="3-deploy-an-azure-application-gateway"></a>3. 部署 Azure 應用程式閘道

您可以從 [使用 Azure 入口網站的路徑型路由規則建立應用程式閘道](../application-gateway/create-url-route-portal.md)，以取得這項功能的詳細指示。 以下是必要步驟的摘要：

1. 在您的訂用帳戶和資源群組中建立虛擬網路。
2. 建立子網 (，作為虛擬網路內的專用子網) 使用。
3. 建立標準應用程式閘道 (選擇性地啟用 WAF) ：從 Azure 入口網站首頁，按一下頁面左上角的 [**資源**  >  **網路**  >  **應用程式閘道**]。 選取標準 SKU 和大小，並提供 Azure 訂用帳戶、資源群組和位置資訊。 如有需要，請為此應用程式閘道建立新的公用 IP，並針對該應用程式閘道的虛擬網路和專用子網提供詳細資料。
4. 新增具有虛擬機器的後端集區，並將它新增至您的應用程式閘道。

## <a name="4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud"></a>4. 在私用雲端中建立及設定 web 伺服器 VM 集區

在 vCenter 中，使用您選擇的作業系統和 web 伺服器來建立 Vm (例如 Windows/IIS 或 Linux/Apache) 。 選擇針對私人雲端中的 web 層所指定的子網/VLAN。 確認 web 伺服器 Vm 至少有一個 vNIC 在 web 層子網中。

---
title: 搭配 VMware 虛擬機器使用 Azure 應用程式閘道
description: 說明如何使用 Azure 應用程式閘道來管理在 VMware 虛擬機器中執行之 web 伺服器的傳入網路流量贏得 AVS 私用雲端環境
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 94cc6e40b88fe631d525f41001034f5dada05397
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77015451"
---
# <a name="use-azure-application-gateway-with-vmware-virtual-machines-in-the-avs-private-cloud-environment"></a>在 AVS 私用雲端環境中搭配 VMware 虛擬機器使用 Azure 應用程式閘道

您可以使用 Azure 應用程式閘道，為您在 AVS 私人雲端環境內的 VMware 虛擬機器中執行的 web 伺服器管理傳入的 web 流量。

藉由運用公用-私人混合部署中的 Azure 應用程式閘道，您可以管理應用程式的網路流量、提供安全的前端，以及卸載在 VMware 環境中執行之服務的 SSL 處理。 Azure 應用程式閘道會根據已設定的規則和健康狀態探查，將連入的網路流量路由傳送至位於 VMware 環境的後端集區實例。

這個 Azure 應用程式的閘道解決方案需要您：

* 擁有 Azure 訂用帳戶。
* 建立和設定 Azure 虛擬網路，以及虛擬網路內的子網。
* 建立並設定 NSG 規則，並使用 ExpressRoute 將您的 vNet 對等互連至您的 AVS 私用雲端。
* 建立 & 設定您的 AVS 私用雲端。
* 建立 & 設定您的 Azure 應用程式閘道。

## <a name="azure-application-gateway-deployment-scenario"></a>Azure 應用程式閘道部署案例

在此案例中，Azure 應用程式閘道會在您的 Azure 虛擬網路中執行。 虛擬網路是透過 ExpressRoute 線路連接到您的 AVS 私人雲端。 AVS 私人雲端中的所有子網都可從虛擬網路子網連線到 IP。

![Azure 虛擬網路中的 azure 負載平衡器](media/load-balancer-use-case.png)

## <a name="how-to-deploy-the-solution"></a>如何部署解決方案

部署套裝程式含下列工作：

1. [確認符合必要條件](#1-verify-prerequisites)
2. [將您的 Azure 虛擬連線連接到 AVS 私人雲端](#2-connect-your-azure-virtual-network-to-your-avs-private-cloud)
3. [部署 Azure 應用程式閘道](#3-deploy-an-azure-application-gateway)
4. [在您的 AVS 私人雲端中建立及設定 Web 服務器 VM 集區](#4-create-and-configure-a-web-server-vm-pool-in-your-avs-private-cloud)

## <a name="1-verify-prerequisites"></a>1. 驗證必要條件

確認符合下列必要條件：

* 已建立 Azure Resource Manager 和虛擬網路。
* 您的 Azure 虛擬網路內已建立專用的子網（適用于應用程式閘道）。
* 已建立 AVS 私用雲端。
* 虛擬網路中的 IP 子網與私人雲端中的子網之間沒有 IP 衝突。

## <a name="2-connect-your-azure-virtual-network-to-your-avs-private-cloud"></a>2. 將 Azure 虛擬網路連接到您的 AVS 私人雲端

若要將 Azure 虛擬網路連線到您的 AVS 私人雲端，請遵循此程式。

1. [在 AVS 入口網站中，複製 ExpressRoute 對等互連資訊](virtual-network-connection.md)。

2. [設定 Azure 虛擬網路的虛擬網路閘道](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)。

3. 將[您的虛擬網路連結到 AVS ExpressRoute 線路](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)。

4. [使用您所複製的對等互連資訊，將您的虛擬網路連結至 ExpressRoute 線路](virtual-network-connection.md)。

## <a name="3-deploy-an-azure-application-gateway"></a>3. 部署 Azure 應用程式閘道

您可以在[使用 Azure 入口網站建立具有路徑型路由規則的應用程式閘道](../application-gateway/create-url-route-portal.md)中取得這項功能的詳細指示。 以下是必要步驟的摘要：

1. 在您的訂用帳戶和資源群組中建立虛擬網路。
2. 在您的虛擬網路內建立子網（用來作為專用子網）。
3. 建立標準應用程式閘道（可選擇啟用 WAF）：從 Azure 入口網站首頁，按一下頁面左上方的 [**資源** > **網路** > **應用程式閘道**]。 選取標準 SKU、大小，並提供 Azure 訂用帳戶、資源群組和位置資訊。 如有需要，請為此應用程式閘道建立新的公用 IP，並提供有關應用程式閘道的虛擬網路和專用子網的詳細資料。
4. 新增具有虛擬機器的後端集區，並將它新增至您的應用程式閘道。

## <a name="4-create-and-configure-a-web-server-vm-pool-in-your-avs-private-cloud"></a>4. 在您的 AVS 私人雲端中建立和設定 web 伺服器 VM 集區

在 vCenter 中，使用您選擇的 OS 和 web 伺服器（例如 Windows/IIS 或 Linux/Apache）來建立 Vm。 選擇在您的 AVS 私人雲端中為 web 層指定的子網/VLAN。 請確認 web 伺服器 Vm 至少有一個 vNIC 位於 web 層子網。

---
title: 將 Azure API 管理與內部虛擬網路一起使用
titleSuffix: Azure API Management
description: 了解如何在內部虛擬網路上安裝和設定 Azure API 管理
services: api-management
documentationcenter: ''
author: vladvino
manager: kjoshi
editor: ''
ms.assetid: dac28ccf-2550-45a5-89cf-192d87369bc3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/31/2019
ms.author: apimpm
ms.openlocfilehash: 6054c595bca26dc2a0432c53369a60a61e3efde0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841858"
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>在內部虛擬網路中使用 Azure API 管理服務
在 Azure 虛擬網路中，Azure API 管理可以管理無法在網際網路上存取的 API。 有許多 VPN 技術可讓您建立連線。 API 管理在虛擬網路內有兩種主要的部署模式：
* 外部
* 內部

當 API 管理在內部虛擬網路模式下部署時，所有服務終結點（代理閘道、開發人員門戶、直接管理和 Git）僅在您控制訪問的虛擬網路中可見。 公用 DNS 伺服器上不會註冊任何服務端點。

> [!NOTE]
> 由於服務終結點沒有 DNS 條目，因此在為虛擬網路[配置 DNS](#apim-dns-configuration)之前，無法訪問這些終結點。

在內部模式中使用 API 管理可讓您實現下列情節：

* 使用站對站或 Azure ExpressRoute VPN 連線，讓裝載於私人資料中心的 API 可供外部第三方安全地存取。
* 透過通用閘道器公開雲端式 API 和內部部署 API，以實現混合式雲端情節。
* 使用單一閘道端點，管理裝載於多個地理位置的 API。

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Prerequisites

若要執行本文所述的步驟，您必須具有：

+ **活動 Azure 訂閱**。

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Azure API 管理執行個體**。 如需詳細資訊，請參閱[建立 Azure API 管理執行個體](get-started-create-service-instance.md)。
+ 在虛擬網路中部署 API 管理服務時，將使用埠清單，並且需要打開這些[埠。](./api-management-using-with-vnet.md#required-ports) 

## <a name="creating-an-api-management-in-an-internal-virtual-network"></a><a name="enable-vpn"> </a>在內部虛擬網路中建立 API 管理
內部虛擬網路中的 API 管理服務託管在[內部負載等化器（經典）](https://docs.microsoft.com/azure/load-balancer/load-balancer-get-started-ilb-classic-cloud)之後。 這是唯一可用的選項，無法更改。

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>使用 Azure 入口網站啟用虛擬網路連線

1. 在 [Azure 入口網站](https://portal.azure.com/)中瀏覽至您的 Azure API 管理執行個體。
2. 選擇**虛擬網路**。
3. 將 API 管理執行個體設定為在虛擬網路內部署。

    ![用於在內部虛擬網路中設定 Azure API 管理的功能表][api-management-using-internal-vnet-menu]

4. 選取 [儲存]****。

部署成功後，您應該在概述欄選項卡上看到 API 管理服務的**專用**虛擬 IP 位址和**公共**虛擬 IP 位址。 **專用**虛擬 IP 位址是從 API 管理委派子網中的負載平衡`gateway`IP 位址，可以`portal`訪問`management``scm`和 終結點。 **公共**虛擬 IP 位址**僅用於**控制從埠 3443 到`management`終結點的平面流量，並可以鎖定到[ApiManagement][ServiceTags]服務標記。

![已設定內部虛擬網路的 API 管理儀表板][api-management-internal-vnet-dashboard]

> [!NOTE]
> Azure 入口網站上可用的測試主控台不適用於**內部** VNET 部署服務，因為並未在公用 DNS 上註冊閘道器 URL。 您應該改為使用**開發人員入口網站**上提供的測試主控台。

### <a name="enable-a-virtual-network-connection-by-using-powershell-cmdlets"></a>使用 PowerShell Cmdlet 啟用虛擬網路連線

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

您也可以使用 PowerShell Cmdlet 來啟用虛擬網路連線能力。

* 在虛擬網路內創建 API 管理服務：使用 Cmdlet [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement)在虛擬網路內創建 Azure API 管理服務，並將其配置為使用內部虛擬網路類型。

* 更新虛擬網路內 API 管理服務的現有部署：使用 Cmdlet [Update-AzApi管理區域](/powershell/module/az.apimanagement/update-azapimanagementregion)在虛擬網路內移動現有 API 管理服務，並將其配置為使用內部虛擬網路類型。

## <a name="dns-configuration"></a><a name="apim-dns-configuration"></a>DNS 組態
當 API 管理處於外部虛擬網路模式時，DNS 是由 Azure 管理。 若是內部虛擬網路模式，您必須管理自己的路由。

> [!NOTE]
> API 管理服務不會接聽 IP 位址傳來的要求。 只有當要求指明其服務端點上所設定的主機名稱時，才會有所回應。 這些端點包括閘道、Azure 入口網站和開發人員入口網站、直接管理端點及 Git。

### <a name="access-on-default-host-names"></a>在預設主機名稱上存取
例如，當您創建名為"contoso內部vnet"的 API 管理服務時，預設情況下配置了以下服務終結點：

   * 閘道或代理：contosointernalvnet.azure-api.net

   * 開發人員門戶：contosointernalvnet.portal.azure-api.net

   * 新的開發人員門戶：contosointernalvnet.developer.azure-api.net

   * 直接管理終結點：contosointernalvnet.management.azure-api.net

   * Git： contosointernalvnet.scm.azure-api.net

若要存取這些 API 管理服務端點，您可以在連線至虛擬網路 (已部署 API 管理) 的子網路中建立虛擬機器。 假設服務的內部虛擬 IP 位址為 10.1.0.5，則可以映射主機檔 %SystemDrive%\驅動程式\等，如下所示：

   * 10.1.0.5 contosointernalvnet.azure-api.net

   * 10.1.0.5 contosointernalvnet.portal.azure-api.net

   * 10.1.0.5 contosointernalvnet.developer.azure-api.net

   * 10.1.0.5 contosointernalvnet.management.azure-api.net

   * 10.1.0.5 contosointernalvnet.scm.azure-api.net

然後，就可以從您建立的虛擬機器存取所有服務端點。
如果在虛擬網路中使用自訂 DNS 伺服器，您也可以建立 A DNS 記錄，並從虛擬網路中的任何地方存取這些端點。

### <a name="access-on-custom-domain-names"></a>在自訂網域名稱上存取

1. 如果不想要以預設主機名稱存取 API 管理服務，您可以為所有服務端點設定自訂網域名稱，如下圖所示：

   ![設定 API 管理的自訂網域][api-management-custom-domain-name]

2. 然後，您可以在 DNS 伺服器中建立記錄，以存取只能從虛擬網路存取的端點。

## <a name="routing"></a><a name="routing"> </a> 路由

* 將從子網範圍保留負載平衡*專用*IP 位址，並用於從虛擬網路內訪問 API 管理服務終結點。 此*專用*IP 位址可在 Azure 門戶中的服務的"概述"邊欄選項卡上找到。 此位址必須註冊到虛擬網路使用的 DNS 伺服器。
* 還將保留負載平衡*的公共*IP 位址 （VIP），以便通過埠 3443 提供對管理服務終結點的訪問。 此*公共*IP 位址可在 Azure 門戶中的服務的"概述"邊欄選項卡上找到。 *公共*IP 位址僅用於控制埠 3443`management`上端點的平面流量，並可以鎖定到[ApiManagement][ServiceTags]服務標記。
* 子網 IP 範圍 （DIP） 中的 IP 位址將分配給服務中的每個 VM，並用於訪問虛擬網路中的資源。 公共 IP 位址 （VIP） 將用於訪問虛擬網路外部的資源。 如果 IP 限制清單用於保護虛擬網路中的資源，則必須指定部署 API 管理服務的子網的整個範圍以授予或限制從服務進行訪問。
* 負載平衡的公共和私人 IP 位址可以在 Azure 門戶中的"概述"邊欄選項卡上找到。
* 如果服務從虛擬網路中刪除，然後重新添加到虛擬網路中，則為公共和私有訪問分配的 IP 位址可能會更改。 如果發生這種情況，可能需要更新虛擬網路中的 DNS 註冊、路由規則和 IP 限制清單。

## <a name="related-content"></a><a name="related-content"> </a>相關內容
如需詳細資訊，請參閱下列文章：
* [在虛擬網路中設定 Azure API 管理時常見的網路設定問題][Common network configuration problems]
* [虛擬網路常見問題解答](../virtual-network/virtual-networks-faq.md)
* [在 DNS 中創建記錄](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10))

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/security-overview.md#service-tags


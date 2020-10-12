---
title: 在內部虛擬網路中使用 Azure API 管理
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
ms.openlocfilehash: ac9554ae5ca151a377395d3b16598f7070c87d10
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87386039"
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>在內部虛擬網路中使用 Azure API 管理服務
在 Azure 虛擬網路中，Azure API 管理可以管理無法在網際網路上存取的 API。 有許多 VPN 技術可讓您建立連線。 API 管理在虛擬網路內有兩種主要的部署模式：
* 外部
* 內部

當 API 管理以內部虛擬網路模式部署時，所有服務端點 (proxy 閘道、開發人員入口網站、直接管理和 Git) 只會顯示在您控制存取的虛擬網路內。 公用 DNS 伺服器上不會註冊任何服務端點。

> [!NOTE]
> 因為服務端點沒有 DNS 專案，所以在設定虛擬網路的 [dns](#apim-dns-configuration) 之前，將無法存取這些端點。

在內部模式中使用 API 管理可讓您實現下列情節：

* 使用站對站或 Azure ExpressRoute VPN 連線，讓裝載於私人資料中心的 API 可供外部第三方安全地存取。
* 透過通用閘道器公開雲端式 API 和內部部署 API，以實現混合式雲端情節。
* 使用單一閘道端點，管理裝載於多個地理位置的 API。

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Prerequisites

若要執行本文所述的步驟，您必須具有：

+ **有效的 Azure 訂**用帳戶。

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Azure API 管理執行個體**。 如需詳細資訊，請參閱[建立 Azure API 管理執行個體](get-started-create-service-instance.md)。
+ 在虛擬網路中部署 API 管理服務時，會使用並需要開啟 [埠清單](./api-management-using-with-vnet.md#required-ports) 。 

## <a name="creating-an-api-management-in-an-internal-virtual-network"></a><a name="enable-vpn"> </a>在內部虛擬網路中建立 API 管理
內部虛擬網路中的 API 管理服務是 [裝載在內部負載平衡器 (傳統) ](/azure/load-balancer/load-balancer-get-started-ilb-classic-cloud)的後方。 這是唯一可用的選項，無法變更。

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>使用 Azure 入口網站啟用虛擬網路連線

1. 在 [Azure 入口網站](https://portal.azure.com/)中瀏覽至您的 Azure API 管理執行個體。
2. 選取 [虛擬網路]。
3. 將 API 管理執行個體設定為在虛擬網路內部署。

    ![用於在內部虛擬網路中設定 Azure API 管理的功能表][api-management-using-internal-vnet-menu]

4. 選取 [儲存]****。

部署成功後，您應該會在總覽分頁上看到 API 管理服務的 **私人** 虛擬 ip 位址和 **公用** 虛擬 ip 位址。 **私人**虛擬 ip 位址是 API 管理委派子網內的負載平衡 ip 位址，可透過其 `gateway` `portal` 存取、 `management` 和 `scm` 端點。 **公用**虛擬 IP 位址**僅**適用于透過埠3443對端點的控制平面流量 `management` ，並且可鎖定至[ApiManagement][ServiceTags] servicetag。

![已設定內部虛擬網路的 API 管理儀表板][api-management-internal-vnet-dashboard]

> [!NOTE]
> Azure 入口網站上可用的測試主控台不適用於**內部** VNET 部署服務，因為並未在公用 DNS 上註冊閘道器 URL。 您應該改為使用**開發人員入口網站**上提供的測試主控台。

### <a name="enable-a-virtual-network-connection-by-using-powershell-cmdlets"></a>使用 PowerShell Cmdlet 啟用虛擬網路連線

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

您也可以使用 PowerShell Cmdlet 來啟用虛擬網路連線能力。

* 在虛擬網路內建立 API 管理服務：使用 Cmdlet [>set-azapimanagement](/powershell/module/az.apimanagement/new-azapimanagement) 在虛擬網路內建立 Azure api 管理服務，並將其設定為使用內部虛擬網路類型。

* 更新虛擬網路內的現有 API 管理服務部署：使用 Cmdlet [AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) 來移動虛擬網路內現有的 api 管理服務，並將其設定為使用內部虛擬網路類型。

## <a name="dns-configuration"></a><a name="apim-dns-configuration"></a>DNS 組態
當 API 管理處於外部虛擬網路模式時，DNS 是由 Azure 管理。 若是內部虛擬網路模式，您必須管理自己的 DNS。

> [!NOTE]
> API 管理服務不會接聽 IP 位址傳來的要求。 只有當要求指明其服務端點上所設定的主機名稱時，才會有所回應。 這些端點包括閘道、Azure 入口網站和開發人員入口網站、直接管理端點及 Git。

### <a name="access-on-default-host-names"></a>在預設主機名稱上存取
例如，當您建立名為 "contosointernalvnet" 的 API 管理服務時，預設會設定下列服務端點：

   * 閘道或 proxy： contosointernalvnet.azure-api.net

   * 開發人員入口網站： contosointernalvnet.portal.azure-api.net

   * 新的開發人員入口網站： contosointernalvnet.developer.azure-api.net

   * 直接管理端點： contosointernalvnet.management.azure-api.net

   * Git： contosointernalvnet.scm.azure-api.net

若要存取這些 API 管理服務端點，您可以在連線至虛擬網路 (已部署 API 管理) 的子網路中建立虛擬機器。 假設服務的內部虛擬 IP 位址是10.1.0.5，您可以對應 hosts 檔案%SystemDrive%\drivers\etc\hosts，如下所示：

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

* 子網範圍中的負載平衡 *私* 用虛擬 IP 位址將會保留，並用於從虛擬網路記憶體取 API 管理服務端點。 此 *私人* IP 位址可在 Azure 入口網站中服務的總覽分頁上找到。 此位址必須向虛擬網路所使用的 DNS 伺服器註冊。
* 也會保留負載平衡的 *公用* IP 位址 (VIP) ，以透過埠3443提供管理服務端點的存取權。 此 *公用* IP 位址可以在 Azure 入口網站中服務的總覽分頁上找到。 *公用*IP 位址只會用於透過埠3443對端點的控制平面流量 `management` ，並且可以鎖定到[ApiManagement][ServiceTags] servicetag。
* 來自子網 IP 範圍 (DIP) 的 IP 位址將會指派給服務中的每個 VM，並將用來存取虛擬網路內的資源。 公用 IP 位址 (VIP) 將用來存取虛擬網路外部的資源。 如果使用 IP 限制清單來保護虛擬網路內的資源，則必須指定要在其中部署 API 管理服務之子網的整個範圍，以授與或限制服務的存取權。
* 負載平衡的公用和私人 IP 位址可以在 Azure 入口網站的總覽分頁上找到。
* 如果服務已從移除，然後再新增回虛擬網路，則指派給公用和私用存取的 IP 位址可能會變更。 如果發生這種情況，可能需要更新虛擬網路內的 DNS 註冊、路由規則和 IP 限制清單。

## <a name="related-content"></a><a name="related-content"> </a>相關內容
如需詳細資訊，請參閱下列文章：
* [在虛擬網路中設定 Azure API 管理時常見的網路設定問題][Common network configuration problems]
* [虛擬網路常見問題](../virtual-network/virtual-networks-faq.md)
* [在 DNS 中建立記錄](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10))

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/security-overview.md#service-tags

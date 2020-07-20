---
title: 在虛擬 WAN 中樞中安裝 Azure 防火牆
titleSuffix: Azure Virtual WAN
description: 在虛擬 WAN 中樞中設定 Azure 防火牆的步驟
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 05/21/2020
ms.author: cherylmc
ms.openlocfilehash: f20ed76a72eecce59a7b8795a42b033230a2f7e0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84753678"
---
# <a name="configure-azure-firewall-in-a-virtual-wan-hub"></a>在虛擬 WAN 中樞中設定 Azure 防火牆

**安全中樞**是具有 Azure 防火牆的 Azure 虛擬 WAN 中樞。 此文章會逐步引導您完成直接從 Azure 虛擬 WAN 入口網站頁面安裝 Azure 防火牆，以將虛擬 WAN 中樞轉換成安全中樞的步驟。

## <a name="before-you-begin"></a>開始之前

此文章中的步驟假設您已經部署具有一或多個中樞的虛擬 WAN。

若要建立新的虛擬 WAN 與新的中樞，請使用下列文章中的步驟：

* [建立虛擬 WAN](virtual-wan-site-to-site-portal.md#openvwan)
* [建立中樞](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-virtual-hubs"></a>檢視虛擬中樞

您虛擬 WAN 的 [概觀] 頁面會顯示虛擬中樞與安全中樞的清單。 下圖顯示沒有安全中樞的虛擬 WAN。

[ ![概觀](./media/howto-firewall/overview.png)](./media/howto-firewall/overview.png#lightbox)

## <a name="convert-to-secured-hub"></a>轉換成安全中樞

1. 在虛擬 WAN 的 [概觀] 頁面上，選取您想要轉換成安全中樞的中樞。 在虛擬中樞頁面上，您會看到有兩個選項可將 Azure 防火牆部署到此中樞上。 請選取其中一個選項。

   [ ![安全性](./media/howto-firewall/security.png)](./media/howto-firewall/security.png#lightbox)

1. 在您選取其中一個選項之後，您會看到 [轉換成安全中樞] 頁面。 選取要轉換的中樞，然後選取位於頁面底部的 [下一步:Azure 防火牆]。

   [ ![選取中樞](./media/howto-firewall/select-hub.png)](./media/howto-firewall/select-hub.png#lightbox)
1. 完成工作流程之後，請選取 [確認]。

   [ ![確認](./media/howto-firewall/confirm.png)](./media/howto-firewall/confirm.png#lightbox)

1. 將中樞轉換成安全中樞之後，您便可以在虛擬 WAN [概觀] 頁面上加以檢視。

   [ ![檢視安全中樞](./media/howto-firewall/secured-hub.png)](./media/howto-firewall/secured-hub.png#lightbox)

## <a name="view-hub-resources"></a>檢視中樞資源

從虛擬 WAN [概觀] 頁面上，選取安全中樞。 在中樞頁面上，您可以檢視所有虛擬中樞資源，包括 Azure 防火牆。

[ ![檢視中樞資源](./media/howto-firewall/view-resources.png)](./media/howto-firewall/view-resources.png#lightbox)

若要從安全中樞檢視 Azure 防火牆設定，請在 [安全性] 底下，選取 [安全的虛擬中樞設定]。
[ ![檢視中樞設定](./media/howto-firewall/hub-settings.png)](./media/howto-firewall/hub-settings.png#lightbox)

## <a name="configure-additional-settings"></a>設定其他設定

若要針對虛擬中樞設定其他 Azure 防火牆設定，請選取 [Azure 防火牆管理員] 的連結。 如需防火牆原則的相關資訊，請參閱 [Azure 防火牆管理員](../firewall-manager/secure-cloud-network.md#create-a-firewall-policy-and-secure-your-hub)。

[ ![其他設定](./media/howto-firewall/additional-settings.png)](./media/howto-firewall/additional-settings.png#lightbox)

若要返回中樞 [概觀] 頁面，您可以按一下路徑來往回瀏覽，如下圖中的箭號所示。

[ ![返回概觀](./media/howto-firewall/arrow.png)](./media/howto-firewall/arrow.png#lightbox)

## <a name="next-steps"></a>後續步驟

如需虛擬 WAN 的詳細資訊，請參閱[常見問題集](virtual-wan-faq.md)。

---
title: Azure VMware Solution by CloudSimple-使用 ExpressRoute 將私人雲端連接到 Azure 網路
description: 說明如何使用 ExpressRoute 將 CloudSimple 私人雲端環境連線至 Azure 虛擬網路
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9bb68ec68f4de646239477ceeaac50a7a33989fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "77015230"
---
# <a name="connect-your-cloudsimple-private-cloud-environment-to-the-azure-virtual-network-using-expressroute"></a>使用 ExpressRoute 將您的 CloudSimple 私用雲端環境連線至 Azure 虛擬網路

您的 CloudSimple 私人雲端可以使用 Azure ExpressRoute 連線到您的 Azure 虛擬網路。  此高頻寬、低延遲連線可讓您從私人雲端環境存取 Azure 訂用帳戶中執行的服務。

虛擬網路連接可讓您：

* 使用 Azure 作為私用雲端上虛擬機器的備份目標。
* 在您的 Azure 訂用帳戶中部署 KMS 伺服器，以加密私用雲端 vSAN 資料存放區。
* 使用混合式應用程式，其中應用程式的 web 層會在公用雲端中執行，而應用程式和資料庫層則是在您的私人雲端中執行。

![虛擬網路的 Azure ExpressRoute 連線](media/cloudsimple-azure-network-connection.png)

## <a name="set-up-a-virtual-network-connection"></a>設定虛擬網路連線

若要設定私人雲端的虛擬網路連線，您需要您的授權金鑰、對等電路 URI 和 Azure 訂用帳戶的存取權。 您可以在 CloudSimple 入口網站的 [虛擬網路連線] 頁面上找到這項資訊。 如需相關指示，請參閱 [取得 Azure 虛擬網路對 CloudSimple 連線的對等互連資訊](virtual-network-connection.md)。 如果您在取得資訊時遇到任何問題，請提交 <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">支援要求</a>。

> [!TIP]
> 如果您已經有 Azure 虛擬網路、閘道子網和虛擬網路閘道，則可以跳至步驟4。

1. 在您的 Azure 訂用帳戶上建立虛擬網路，並確認您選取的位址空間與您私人雲端的位址空間不同。  如果您已經有 Azure 虛擬網路，您可以使用現有的虛擬網路。  如需詳細資訊，請參閱 [使用 Azure 入口網站建立虛擬網路](../virtual-network/quick-create-portal.md)。
2. 在您的 Azure 虛擬網路上建立閘道子網。  如果您的 Azure 虛擬網路中已經有閘道子網，您可以使用現有的子網。 如需詳細資訊，請參閱 [建立閘道子網](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet)。
3. 在您的虛擬網路上建立虛擬網路閘道。  如果您有現有的虛擬網路閘道，您可以使用現有的虛擬網路閘道。 如需詳細資訊，請參閱 [建立虛擬網路閘道](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway)。
4. 藉由兌換授權金鑰（如 [將虛擬網路連線至線路不同的訂](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)用帳戶中所述），在您的虛擬網路與私人雲端之間建立連線。

> [!WARNING]
> 如果您使用現有的虛擬網路閘道，而且它具有與 CloudSimple ExpressRoute 線路相同位置的 ExpressRoute 連線，將不會建立連接。  建立新的虛擬網路，並遵循先前的步驟。

## <a name="test-the-virtual-network-connection"></a>測試虛擬網路連接

建立連線之後，您可以在 [**設定**] 底下選取 [**屬性**]，以檢查連線的狀態。  狀態和布建狀態應該會顯示為 [ **成功**]。

![連接狀態](media/azure-expressroute-connection.png)

若要測試虛擬網路連接：

1. 在您的 Azure 訂用帳戶中建立虛擬機器。
2. 尋找私人雲端 vCenter 的 IP 位址 (請參閱您的歡迎電子郵件) 。
3. 從 Azure 虛擬網路中建立的虛擬機器偵測您的雲端 vCenter。
4. 從在私人雲端 vCenter 中執行的虛擬機器偵測您的 Azure 虛擬機器。

如果您在建立連接時遇到任何問題，請提交 <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">支援要求</a>。

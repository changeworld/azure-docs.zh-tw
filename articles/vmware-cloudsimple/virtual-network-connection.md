---
title: 使用 ExpressRoute 將 Azure 虛擬網路連線到 CloudSimple-Azure VMware Solution by CloudSimple
description: 說明如何取得 Azure 虛擬網路與 CloudSimple 環境之間連線的對等互連資訊
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6b20ee4e04a4443529ecceca8c6fc2206f7df39d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "77563986"
---
# <a name="connect-azure-virtual-network-to-cloudsimple-using-expressroute"></a>使用 ExpressRoute 將 Azure 虛擬網路連線到 CloudSimple

您可以將私人雲端網路延伸至 Azure 虛擬網路和 Azure 資源。 ExpressRoute 連線可讓您從私人雲端存取 Azure 訂用帳戶中執行的資源。

## <a name="request-authorization-key"></a>要求授權金鑰

您的私人雲端與 Azure 虛擬網路之間的 ExpressRoute 連線需要授權金鑰。 若要取得金鑰，請提出 <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">支援</a>的票證。  在要求中使用下列資訊：

* 問題類型： **技術**
* 訂用帳戶：**選取部署 CloudSimple 服務的訂**用帳戶
* 服務： **VMware Solution By CloudSimple**
* 問題類型： **服務要求**
* 問題子類型： **AZURE VNET 連接的授權金鑰**
* 主體： **AZURE VNET 連線的授權金鑰要求**

## <a name="get-peering-information-from-cloudsimple-portal"></a>從 CloudSimple 入口網站取得對等互連資訊

若要設定連接，您必須建立 Azure 虛擬網路與 CloudSimple 環境之間的連線。  在程式的過程中，您必須提供對等電路 URI 和授權金鑰。 從 [CloudSimple 入口網站](access-cloudsimple-portal.md)取得 URI 和授權金鑰。  選取側邊功能表上的 [ **網路** ]，然後選取 [ **Azure 網路**連線]。 或選取側邊功能表上的 [ **帳戶** ]，然後選取 [ **Azure 網路**連線]。

使用 *複製* 圖示來複製對等電路 URI 和每個區域的授權金鑰。 針對您想要連接的每個 CloudSimple 區域：

1. 按一下 [ **複製** ] 以複製 URI。 將它貼入可供新增至 Azure 入口網站的檔案中。  
2. 按一下 [ **複製** ]，將授權金鑰複製並貼到檔案中。

複製處於 **可用** 狀態的授權金鑰和對等電路 URI。  [已**使用**] 狀態表示已使用金鑰建立虛擬網路連接。

![虛擬網路連接頁面](media/virtual-network-connection.png)

如需有關設定 Azure 虛擬網路 CloudSimple 連結的詳細資訊，請參閱 [使用 ExpressRoute 將您的 CloudSimple 私用雲端環境連線至 Azure 虛擬網路](azure-expressroute-connection.md)。

## <a name="next-steps"></a>後續步驟

* [私人雲端的 Azure 虛擬網路連線](azure-expressroute-connection.md)
* [使用 Azure ExpressRoute 連接到內部部署網路](on-premises-connection.md)

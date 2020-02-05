---
title: 使用 ExpressRoute 將 Azure 虛擬網路連接到 AVS
description: 說明如何取得 Azure 虛擬網路與您的 AVS 環境之間連線的對等資訊
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1ff11bbafe6f9057ce70c799e0437691d89ccb40
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014397"
---
# <a name="connect-azure-virtual-network-to-avs-using-expressroute"></a>使用 ExpressRoute 將 Azure 虛擬網路連接到 AVS

您可以將您的 AVS 私人雲端網路擴充至您的 Azure 虛擬網路和 Azure 資源。 ExpressRoute 連線可讓您從您的 AVS 私人雲端存取 Azure 訂用帳戶中執行的資源。

## <a name="request-authorization-key"></a>要求授權金鑰

您的 AVS 私人雲端與 Azure 虛擬網路之間的 ExpressRoute 連線需要授權金鑰。 若要取得金鑰，請向<a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">支援</a>提出票證。 在要求中使用下列資訊：

* 問題類型：**技術**
* 訂用帳戶：**選取要部署 AVS 服務的訂**用帳戶
* 服務： **VMware 解決方案（AVS）**
* 問題類型：**服務要求**
* 問題子類型： **AZURE VNET 連線的授權金鑰**
* 主旨：**要求 AZURE VNET 連線的授權金鑰**

## <a name="get-peering-information-from-avs-portal"></a>從 AVS 入口網站取得對等資訊

若要設定連線，您必須建立 Azure 虛擬網路與您的 AVS 環境之間的連線。 在此過程中，您必須提供對等線路 URI 和授權金鑰。 從[AVS 入口網站](access-cloudsimple-portal.md)取得 URI 和授權金鑰。 選取側邊功能表上的 [**網路**]，然後選取 [ **Azure 網路**連線]。 或選取側邊功能表上的 [**帳戶**]，然後選取 [ **Azure 網路**連線]。

使用*複製*圖示，為每個區域的授權金鑰複製對等線路 URI 和。 針對您想要連接的每個 AVS 區域：

1. 按一下 [**複製**] 以複製 URI。 將它貼入可供新增至 Azure 入口網站的檔案中。 
2. 按一下 [**複製**] 來複製授權金鑰，並將它貼到檔案中。

複製處於**可用**狀態的授權金鑰和對等線路 URI。 [已**使用**] 狀態表示金鑰已經用來建立虛擬網路連線。

![虛擬網路連接 頁面](media/virtual-network-connection.png)

如需設定 Azure 虛擬網路至 AVS 連結的詳細資訊，請參閱[使用 ExpressRoute 將您的 AVS 私用雲端環境連接到 Azure 虛擬網路](azure-expressroute-connection.md)。

## <a name="next-steps"></a>後續步驟

* [Azure 虛擬網路與 AVS 私人雲端的連線](azure-expressroute-connection.md)
* [使用 Azure ExpressRoute 連接到內部部署網路](on-premises-connection.md)

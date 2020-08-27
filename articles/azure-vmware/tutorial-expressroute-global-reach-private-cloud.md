---
title: 將內部部署環境對等互連至私人雲端
description: 在此 Azure VMware 解決方案教學課程中，您會在 Azure VMware 解決方案中建立與私人雲端對等互連的 ExpressRoute Global Reach。
ms.topic: tutorial
ms.date: 07/16/2020
ms.openlocfilehash: db3f5988cb8c07d9b6e80f500ac6aff8f96dfded
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750442"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>教學課程：將內部部署環境對等互連至私人雲端

ExpressRoute Global Reach 可將您的內部部署環境連線至私人雲端。 在私人雲端 ExpressRoute 線路與內部部署環境的現有 ExpressRoute 連線之間，會建立 ExpressRoute Global Reach 連線。  有一些指示可讓您使用 Azure CLI 和 PowerShell 來設定 ExpressRoute Global Reach，而且我們已透過特定詳細資料和範例增強 [CLI 命令](../expressroute/expressroute-howto-set-global-reach-cli.md)，以協助您設定內部部署環境與 Azure VMware 解決方案私人雲端之間的 ExpressRoute Global Reach 對等互連。   

在使用 ExpressRoute Global Reach 啟用兩個 ExpressRoute 線路之間的連線之前，請檢閱有關如何[在不同 Azure 訂用帳戶中啟用連線](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions)的文件。  您在[設定 Azure 到私人雲端的網路](tutorial-configure-networking.md)時所使用的 ExpressRoute 線路，需要您在對等互連至 ExpressRoute 閘道或使用 Global Reach 與其他 ExpressRoute 線路對等互連時，建立並使用授權金鑰。 您已使用 ExpressRoute 線路中的一個授權金鑰，而您將建立第二個授權金鑰，以便與內部部署 ExpressRoute 線路對等互連。

> [!TIP]
> 在這些指示的內容中，您的內部部署 ExpressRoute 線路是_線路 1_，而您的私人雲端 ExpressRoute 線路會在不同的訂用帳戶中，並標示為_線路 2_。 

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 利用現有指示來管理 ExpressRoute 線路和 ExpressRoute Global Reach 對等互連
> * 為_線路 2_ (私人雲端 ExpressRoute 線路) 建立授權金鑰
> * 在_線路 1_ 的訂用帳戶中，於 Cloud Shell 中使用 Azure CLI 來啟用內部部署對私人雲端的 ExpressRoute Global Reach 對等互連

## <a name="prerequisites"></a>必要條件

本教學課程的必要條件如下：
- 一個私人雲端，其 ExpressRoute 線路已與 Azure 虛擬網路 (VNet) 中的 ExpressRoute 閘道對等互連 – 從對等互連程序的角度來看，這是_線路 2_。
- 不同的運作中 ExpressRoute 線路，以便用來將內部部署環境連線至 Azure – 從對等互連程序的角度來看，這是_線路 1_。
- /29 的非重疊[網路位址區塊](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings)，用於進行 ExpressRoute Global Reach 對等互連。

## <a name="create-an-expressroute-authorization-key-in-the-azure-vmware-solution-private-cloud"></a>在 Azure VMware 解決方案私人雲端中建立 ExpressRoute 授權金鑰

1. 從私人雲端 [概觀]的 [管理] 底下，選取 [連線] > [ExpressRoute] > [要求授權金鑰]。

   :::image type="content" source="media/expressroute-global-reach/start-request-auth-key.png" alt-text="選取 [連線] > [ExpressRoute] > [要求授權金鑰] 以開始新的要求。":::

2. 輸入授權金鑰的名稱，然後選取 [建立]。 

   :::image type="content" source="media/expressroute-global-reach/create-global-reach-auth-key.png" alt-text="按一下 [建立] 以建立新的授權金鑰。":::

   建立之後，新的金鑰會出現在私人雲端的授權金鑰清單中。 

   :::image type="content" source="media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="確認新的授權金鑰出現在私人雲端的金鑰清單中。":::

3. 記下授權金鑰和 ExpressRoute 識別碼，以及 /29 位址區塊。 您將在下一個步驟中使用這些資料來完成對等互連。 

## <a name="peer-private-cloud-to-on-premises-using-authorization-key"></a>使用授權金鑰將私人雲端對等互連至內部部署環境

您已建立私人雲端 ExpressRoute 線路的授權金鑰，接下來可以讓其與您的內部部署 ExpressRoute 線路對等互連。  從內部部署 ExpressRoute 線路的觀點來看，您可以使用 Cloud Shell 中的 Azure CLI 以及資源識別碼和私人雲端 ExpressRoute 線路的授權金鑰 (已在前面的步驟中建立) 來完成對等互連。

> [!TIP]  
> 為了簡單起見，在 Azure CLI 的命令輸出中，這些指示可能會使用 [`–query` 引數來執行 JMESPath 查詢，從而只顯示所需的結果](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest)。


1. 使用與內部部署 ExpressRoute 線路相同的訂用帳戶登入 Azure 入口網站，然後開啟 Cloud Shell。 讓殼層保持是 Bash。
 
   :::image type="content" source="media/expressroute-global-reach/open-cloud-shell.png" alt-text="登入 Azure 入口網站並開啟 Cloud Shell。":::
 
2. 在命令列上，輸入 Azure CLI 命令來建立對等互連 (使用您的特定資訊和資源識別碼、授權金鑰與 /29 CIDR 網路區塊)。 

   以下是您將使用的命令範例，以及指出對等互連成功的輸出。 範例命令是以[「在不同 Azure 訂用帳戶中啟用 ExpressRoute 線路之間的連線」的步驟 3](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions) 中所使用的命令作為基礎。

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="在 Cloud Shell 中使用 Azure CLI 命令建立 ExpressRoute Global Reach 對等互連。":::
 
   您現在應該能夠透過 ExpressRoute Global Reach 對等互連，從內部部署環境連線至您的私人雲端。

> [!TIP]
> 您可以遵循[停用內部部署網路之間的連線](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks)來刪除您剛才建立的對等互連。


## <a name="next-steps"></a>後續步驟

如果您打算使用混合式雲端擴充功能 (HCX) 將 VM 工作負載遷移至私人雲端，請使用[安裝適用於 Azure VMware Solution 的 HCX](hybrid-cloud-extension-installation.md)程序。


<!-- LINKS - external-->

<!-- LINKS - internal -->

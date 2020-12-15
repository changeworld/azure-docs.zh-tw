---
title: 將 ExpressRoute 連線到虛擬網路閘道
description: 將 ExpressRoute 連線到虛擬網路閘道的步驟。
ms.topic: include
ms.date: 12/08/2020
ms.openlocfilehash: 5f9a565a7662041dbd85e61388129496fa376962
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861507"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-configure-networking.md -->

1. 瀏覽至您在＜[在 Azure 中部署 vSphere 叢集](../tutorial-create-private-cloud.md)＞教學課程中建立的私人雲端。 選取 [管理] 底下的 [連線]，然後選取 [ExpressRoute] 索引標籤。

1. 複製授權金鑰。 如果沒有授權金鑰，您需要建立一個，然後選取 [+ 要求授權金鑰]。

   :::image type="content" source="../media/expressroute-global-reach/start-request-auth-key.png" alt-text="複製授權金鑰。如果沒有授權金鑰，您需要建立一個，然後選取 [+ 要求授權金鑰]。" border="true" lightbox="../media/expressroute-global-reach/start-request-auth-key.png":::

1. 瀏覽至您在上一個步驟中建立的虛擬網路閘道，然後在 [設定]下，選取 [連線]。 在 [連線] 頁面上，選取 [+新增]。

1. 在 [新增連線] 頁面上，提供欄位的值，然後選取 [確定]。 

   | 欄位 | 值 |
   | --- | --- |
   | **名稱**  | 輸入連線的名稱。  |
   | **連線類型**  | 選取 [ExpressRoute]。  |
   | **兌換授權**  | 請務必選取此方塊。  |
   | **虛擬網路閘道** | 您先前建立的虛擬網路閘道。  |
   | **授權金鑰**  | 複製並貼上您資源群組的 ExpressRoute 索引標籤中的授權金鑰。 |
   | **對等線路 URI**  | 複製並貼上您資源群組的 ExpressRoute 索引標籤中的 ExpressRoute 識別碼。  |

   :::image type="content" source="../media/expressroute-global-reach/open-cloud-shell.png" alt-text="在 [新增連線] 頁面上，提供欄位的值，然後選取 [確定]。" border="true" lightbox="../media/expressroute-global-reach/open-cloud-shell.png":::

即會在您的 ExpressRoute 線路與虛擬網路之間建立連線。
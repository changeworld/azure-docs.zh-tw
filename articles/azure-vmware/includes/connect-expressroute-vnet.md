---
title: 將 ExpressRoute 連線到虛擬網路閘道
description: 將 ExpressRoute 連線到虛擬網路閘道的步驟。
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 214ef9c01193b238c8e456ef2809f7a2edbdb6c7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91598186"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-configure-networking.md -->

1. 瀏覽至您在上一個教學課程中建立的私ㄟ雲端，然後選取 [管理] 下的 [連線]，選取 [ExpressRoute] 索引標籤。

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

   :::image type="content" source="../media/expressroute-global-reach/open-cloud-shell.png" alt-text="複製授權金鑰。如果沒有授權金鑰，您需要建立一個，然後選取 [+ 要求授權金鑰]。" border="true" lightbox="../media/expressroute-global-reach/open-cloud-shell.png":::

即會在您的 ExpressRoute 線路與虛擬網路之間建立連線。
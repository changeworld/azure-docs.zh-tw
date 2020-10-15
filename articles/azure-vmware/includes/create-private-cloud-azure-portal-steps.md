---
title: 部署 Azure VMware 解決方案
description: 使用 Azure 入口網站部署 Azure VMware 解決方案的步驟。
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 4d0881721cd8c13d1b6c9fb3a29e4cdb6d6a753f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91578305"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 選取 [建立新的資源]。 在 [搜尋 Marketplace] 文字方塊中，輸入 `Azure VMware Solution`，然後從清單中選取 [Azure VMware 解決方案]。 在 [Azure VMware 解決方案] 視窗中，選取 [建立]。

1. 在 [基本概念] 索引標籤上，輸入欄位的值。 下表列出欄位的屬性。

   | 欄位   | 值  |
   | ---| --- |
   | **訂用帳戶** | 您計畫用於部署的訂用帳戶。|
   | **資源群組** | 私人雲端資源的資源群組。 |
   | **位置** | 選取一個位置，例如 [美國東部]。|
   | **資源名稱** | Azure VMware 解決方案私人雲端的名稱。 |
   | **SKU** | 選取下列 SKU 值：AV36 |
   | **主控件** | 要新增至私人雲端叢集的主機數目。 預設值為 3，您可以在部署之後提高或降低此值。  |
   | **vCenter 管理密碼** | 輸入雲端管理員密碼。 |
   | **NSX-T 管理員密碼** | 輸入 NSX-T 管理員密碼。 |
   | **位址封鎖** | 為私人雲端輸入 CIDR 網路的 IP 位址區塊，例如，10.175.0.0/22。 |
   | **虛擬網路** | 選取虛擬網路或為 Azure VMware 解決方案私人雲端建立一個新的虛擬網路。  |

   :::image type="content" source="../media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="在 [基本資料] 索引標籤上，輸入欄位的值。" border="true":::

1. 在完成後，選取 [檢閱+建立]。 在下一個畫面上，驗證輸入的資訊。 如果資訊都正確無誤，請選取 [建立]。

   > [!NOTE]
   > 這個步驟需要大約 2 小時。 

1. 驗證部署是否成功。 瀏覽至您所建立的資源群組，然後選取您的私人雲端。  當部署完成時，您會看到 [成功] 的狀態。 

   :::image type="content" source="../media/tutorial-create-private-cloud/validate-deployment.png" alt-text="在 [基本資料] 索引標籤上，輸入欄位的值。" border="true":::
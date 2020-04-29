---
title: 使用現有的 Azure Load Balancer Azure 入口網站設定虛擬機器擴展集
description: 瞭解如何使用現有的 Azure Load Balancer 來設定虛擬機器擴展集。
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: b2a83b226a4b2ddbbd554783de6a2b5c85c92f0a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80349713"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-portal"></a>使用 Azure 入口網站設定具有現有 Azure Load Balancer 的虛擬機器擴展集

在本文中，您將瞭解如何使用現有的 Azure Load Balancer 來設定虛擬機器擴展集。 

## <a name="prerequisites"></a>Prerequisites

- Azure 訂用帳戶。
- 訂用帳戶中的現有標準 sku 負載平衡器，將會在其中部署虛擬機器擴展集。
- 適用于虛擬機器擴展集的 Azure 虛擬網路。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。



## <a name="deploy-virtual-machine-scale-set-with-existing-load-balancer"></a>使用現有的負載平衡器部署虛擬機器擴展集

在本節中，您會在 Azure 入口網站中使用現有的 Azure 負載平衡器建立虛擬機器擴展集。

> [!NOTE]
> 下列步驟假設先前已部署名為**myVNet**的虛擬網路和名為**myLoadBalancer**的 Azure 負載平衡器。

1. 在畫面的左上方，按一下 [**建立資源** > ] [**計算** > ] [**虛擬機器擴展集**] 或 [在 marketplace 搜尋中搜尋**虛擬機器擴展集**]。

2. 選取 [建立]  。

3. 在 [**建立虛擬機器擴展集**] 的 [**基本**] 索引標籤中，輸入或選取這項資訊：

    | 設定                        | 值                                                                                                 |
    |--------------------------------|-------------------------------------------------------------------------------------------------------|
    | **專案詳細資料**            |                                                                                                       |
    | 訂用帳戶                   | 選取您的 Azure 訂用帳戶                                                                        |
    | 資源群組                 | 選取 [新建]，輸入**myResourceGroup**，然後選取 [確定]，或選取現有的資源群組。 |
    | **擴展集詳細資料**          |                                                                                                       |
    | 虛擬機器擴展集名稱 | 輸入**myVMSS**                                                                                      |
    | 區域                         | 選取 [**美國東部 2** ]                                                                                    |
    | 可用性區域              | 選取**無**                                                                                       |
    | **執行個體詳細資料**           |                                                                                                       |
    | Image                          | 選取**Ubuntu Server 18.04 LTS**                                                                    |
    | Azure 點實例            | 選取 [否]****                                                                                         |
    | Size                           | 保留為預設值                                                                                      |
    | **系統管理員帳戶**      |                                                                                                       |
    | 驗證類型            | 選取**密碼**                                                                                   |
    | 使用者名稱                       | 輸入您的系統管理員使用者名稱        |
    | 密碼                       | 輸入您的系統管理員密碼    |
    | 確認密碼               | 重新輸入您的系統管理員密碼 |


    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-01.png" alt-text="建立虛擬機器擴展集。" border="true":::

4. 選取 [**網路**] 索引標籤。

5. 在 [**網路**功能] 索引標籤中，輸入或選取這項資訊：

     設定                           | 值                                                    |
    |-----------------------------------|----------------------------------------------------------|
    | **虛擬網路組態** |                                                          |
    | 虛擬網路                   | 選取 [ **myVNet** ] 或您現有的虛擬網路。      |
    | **負載平衡**                |                                                          |
    | 使用負載平衡器               | 選取 [是]****。                                           |
    | **負載平衡設定**       |                                                          |
    | 負載平衡選項            | 選取**Azure 負載平衡器**                           |
    | 選取負載平衡器            | 選取**myLoadBalancer**或您現有的負載平衡器 |
    | 選取後端集區             | 選取**myBackendPool**或您現有的後端集區。  |

    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-02.png" alt-text="建立虛擬機器擴展集。" border="true":::

6. 選取 [**管理**] 索引標籤。

7. 在 [**管理**] 索引標籤中，將 [**開機診斷**] 設定為**關閉**。

8. 選取 [藍色**審核] [+ 建立**] 按鈕。

9. 檢查設定，然後選取 [**建立**] 按鈕。

## <a name="next-steps"></a>後續步驟

在本文中，您已使用現有的 Azure Load Balancer 來部署虛擬機器擴展集。  若要深入瞭解虛擬機器擴展集和負載平衡器，請參閱：

- [什麼是 Azure Load Balancer？](load-balancer-overview.md)
- [什麼是虛擬機器擴展集？](../virtual-machine-scale-sets/overview.md)

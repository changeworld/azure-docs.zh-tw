---
title: 使用現有 Azure 負載等化器配置虛擬機器規模集 - Azure 門戶
description: 瞭解如何使用現有 Azure 負載等化器配置虛擬機器規模集。
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: b2a83b226a4b2ddbbd554783de6a2b5c85c92f0a
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349713"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-portal"></a>使用 Azure 門戶使用現有 Azure 負載等化器配置虛擬機器規模集

在本文中，您將瞭解如何使用現有的 Azure 負載等化器配置虛擬機器規模集。 

## <a name="prerequisites"></a>Prerequisites

- Azure 訂用帳戶。
- 將部署虛擬機器規模集的訂閱中的現有標準 sKU 負載等化器。
- 虛擬機器縮放集的 Azure 虛擬網路。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登錄到 中的[https://portal.azure.com](https://portal.azure.com)Azure 門戶。



## <a name="deploy-virtual-machine-scale-set-with-existing-load-balancer"></a>使用現有負載等化器部署虛擬機器規模集

在本節中，您將使用現有的 Azure 負載等化器在 Azure 門戶中創建虛擬機器縮放集。

> [!NOTE]
> 以下步驟假定以前已部署名為**myVNet**的虛擬網路和名為**myLoadBalancer 的**Azure 負載等化器。

1. 在螢幕的左上角，按一下"**創建資源** > **計算** > **虛擬機器比例集**"或搜索市場搜索中設置的**虛擬機器比例。**

2. 選取 [建立]****。

3. 在 **"創建虛擬機器縮放集**"中，輸入或選擇"**基礎知識"** 選項卡中的此資訊：

    | 設定                        | 值                                                                                                 |
    |--------------------------------|-------------------------------------------------------------------------------------------------------|
    | **專案詳情**            |                                                                                                       |
    | 訂用帳戶                   | 選取您的 Azure 訂用帳戶                                                                        |
    | 資源群組                 | 選擇"創建新"，輸入**我的資源組**，然後選擇"確定"，或選擇現有資源組。 |
    | **縮放設置詳細資訊**          |                                                                                                       |
    | 虛擬機器擴展集名稱 | 輸入**myVMSS**                                                                                      |
    | 區域                         | 選取 [美國東部 2]****。                                                                                    |
    | 可用性區域              | 選擇 **"無"**                                                                                       |
    | **實例詳細資訊**           |                                                                                                       |
    | 映像                          | 選擇**Ubuntu 伺服器 18.04 LTS**                                                                    |
    | Azure Spot 實例            | 選取 [否]****                                                                                         |
    | 大小                           | 預設保留                                                                                      |
    | **管理員帳戶**      |                                                                                                       |
    | 驗證類型            | 選擇**密碼**                                                                                   |
    | 使用者名稱                       | 輸入管理員使用者名        |
    | 密碼                       | 輸入管理員密碼    |
    | 確認密碼               | 重新輸入管理員密碼 |


    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-01.png" alt-text="創建虛擬機器縮放集。" border="true":::

4. 選擇 **"網路"** 選項卡。

5. 在 **"網路"** 選項卡中輸入或選擇此資訊：

     設定                           | 值                                                    |
    |-----------------------------------|----------------------------------------------------------|
    | **虛擬網路組態** |                                                          |
    | 虛擬網路                   | 選擇**myVNet**或您現有的虛擬網路。      |
    | **負載平衡**                |                                                          |
    | 使用負載等化器               | 選取 [是]****。                                           |
    | **負載平衡設定**       |                                                          |
    | 負載平衡選項            | 選擇**Azure 負載等化器**                           |
    | 選擇負載等化器            | 選擇**myLoad 平衡器**或現有負載等化器 |
    | 選擇後端池             | 選擇 **"我的後端池**"或"現有後端池"。  |

    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-02.png" alt-text="創建虛擬機器縮放集。" border="true":::

6. 選擇"**管理**"選項卡。

7. 在 **"管理"** 選項卡中，將 **"啟動診斷**"設置為 **"關閉**"。

8. 選擇藍色 **"審閱 + 創建**"按鈕。

9. 查看設置並選擇"**創建**"按鈕。

## <a name="next-steps"></a>後續步驟

在本文中，您部署了一個虛擬機器縮放集與現有的 Azure 負載等化器。  要瞭解有關虛擬機器縮放集和負載等化器的更多詳細資訊，請參閱：

- [什麼是 Azure Load Balancer？](load-balancer-overview.md)
- [什麼是虛擬機器擴展集？](../virtual-machine-scale-sets/overview.md)

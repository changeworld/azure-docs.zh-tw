---
title: 將 Azure 功能與 Azure 虛擬網路集成
description: 分步教程，演示如何將函數連接到 Azure 虛擬網路
author: alexkarcher-msft
ms.topic: article
ms.date: 5/03/2019
ms.author: alkarche
ms.reviewer: glenga
ms.openlocfilehash: 0c70c69f547405eb8ebdcf6dcc6ae597db151e53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75433221"
---
# <a name="tutorial-integrate-functions-with-an-azure-virtual-network"></a>教學課程：將 Functions 與 Azure 虛擬網路整合

本教程演示如何使用 Azure 函數連接到 Azure 虛擬網路中的資源。 您將創建一個函數，該函數可以訪問 Internet 和在虛擬網路中運行 WordPress 的 VM。

> [!div class="checklist"]
> * 在高級計畫中創建函數應用
> * 將 WordPress 網站部署到虛擬網路中的 VM
> * 將功能應用連接到虛擬網路
> * 創建函數代理以訪問 WordPress 資源
> * 從虛擬網路內部請求 WordPress 檔

## <a name="topology"></a>拓撲

下圖顯示了您創建的解決方案的體系結構：

 ![用於虛擬網路集成的 UI](./media/functions-create-vnet/topology.png)

在高級計畫中運行的功能具有與 Azure 應用服務中的 Web 應用相同的託管功能，其中包括 VNet 集成功能。 要瞭解有關 VNet 集成（包括故障排除和高級配置）的更多資訊，請參閱[將應用與 Azure 虛擬網路集成](../app-service/web-sites-integrate-with-vnet.md)。

## <a name="prerequisites"></a>Prerequisites

在本教程中，瞭解 IP 定址和子網非常重要。 你可以從本文開始[，介紹定址和子網的基礎知識](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics)。 更多文章和視頻可線上獲取。

如果沒有 Azure 訂閱，請先創建[一個免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。"

## <a name="create-a-function-app-in-a-premium-plan"></a>在高級計畫中創建函數應用

首先，在[高級計畫中]創建函數應用。 此計畫提供無伺服器擴展，同時支援虛擬網路集成。

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

您可以通過選擇右上角的固定圖示將功能應用固定到儀表板。 固定使創建 VM 後更容易返回到此函數應用。

## <a name="create-a-vm-inside-a-virtual-network"></a>在虛擬網路內創建 VM

接下來，創建一個預配置的 VM，在虛擬網路內運行 WordPress（通過 Jetware 運行[WordPress LEMP7 最大性能](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure)）。 WordPress VM 的使用是因為它成本低且方便。 此相同的方案適用于虛擬網路中的任何資源，如 REST API、應用服務環境和其他 Azure 服務。 

1. 在門戶中，選擇 " 在左側功能窗格中，在搜索欄位類型`WordPress LEMP7 Max Performance`中**創建資源**"，然後按 Enter。

1. 在搜尋結果中選擇 **"字壓 LEMP 最大性能**"。 選擇**Wordpress LEMP CentOS 的最大性能**軟體計畫作為**軟體計畫**，然後選擇"**創建**"。

1. 在 **"基礎知識"** 選項卡中，使用圖像下表中指定的 VM 設置：

    ![用於創建 VM 的基礎知識選項卡](./media/functions-create-vnet/create-vm-1.png)

    | 設定      | 建議的值  | 描述      |
    | ------------ | ---------------- | ---------------- |
    | **訂閱** | 您的訂用帳戶 | 創建資源的訂閱。 | 
    | **[資源組](../azure-resource-manager/management/overview.md)**  | myResourceGroup | 選擇`myResourceGroup`，或者選擇使用函數應用創建的資源組。 對函數應用 WordPress VM 和託管計畫使用相同的資源組，在完成本教程後，可以更輕鬆地清理資源。 |
    | **虛擬機器名稱** | VNET-文字印刷機 | VM 名稱在資源組中必須是唯一的 |
    | **[地區](https://azure.microsoft.com/regions/)** | （歐洲）西歐 | 選擇您附近的區域或訪問 VM 的功能附近的區域。 |
    | **大小** | B1s | 選擇 **"更改大小**"，然後選擇具有 1 個 vCPU 和 1 GB 記憶體的 B1 標準映射。 |
    | **身份驗證類型** | 密碼 | 要使用密碼身份驗證，還必須指定**使用者名**、安全**密碼**，然後**確認密碼**。 在本教程中，除非需要疑難排解，否則無需登錄到 VM。 |

1. 選擇"**網路**"選項卡，在"配置虛擬網路"下選擇 **"創建新**"。

1. 在 **"創建虛擬網路"** 中，使用圖像下方表中的設置：

    ![創建 VM 的網路選項卡](./media/functions-create-vnet/create-vm-2.png)

    | 設定      | 建議的值  | 描述      |
    | ------------ | ---------------- | ---------------- |
    | **名稱** | 我的資源組-vnet | 您可以使用為虛擬網路生成的預設名稱。 |
    | **位址範圍** | 10.10.0.0/16 | 對虛擬網路使用單個位址範圍。 |
    | **子網名稱** | 教程網 | 子網路的名稱。 |
    | **位址範圍**（子網） | 10.10.1.0/24   | 子網大小定義可以添加到子網的介面數。 WordPress 網站使用此子網。  `/24`子網提供 254 個主機位址。 |

1. 選擇 **"確定"** 以創建虛擬網路。

1. 返回 **"網路"** 選項卡，為公共**IP**選擇 **"無**"。

1. 選擇 **"管理**"選項卡，然後在**診斷存儲帳戶**中，選擇使用函數應用創建的存儲帳戶。

1. 選擇 **"審閱" = 創建**。 驗證完成後，選擇 **"創建**"。 VM 創建過程需要幾分鐘時間。 創建的 VM 只能訪問虛擬網路。

1. 創建 VM 後，選擇 **"轉到資源"** 以查看新 VM 的頁面，然後在 **"設置"** 下選擇 **"網路**"。

1. 驗證沒有**公共 IP。** 記下**專用 IP**，用於從函數應用連接到 VM。

    ![VM 中的網路設置](./media/functions-create-vnet/vm-networking.png)

現在，WordPress 網站完全部署在虛擬網路中。 本網站無法從公共互聯網上訪問。

## <a name="connect-your-function-app-to-the-virtual-network"></a>將功能應用連接到虛擬網路

在虛擬網路中的 VM 中運行 WordPress 網站後，您現在可以將函數應用連接到該虛擬網路。

1. 在新功能應用中，選擇**平臺功能** > **"網路**"。

    ![在功能應用中選擇網路](./media/functions-create-vnet/networking-0.png)

1. 在**VNet 集成**下，選擇 **"按一下此處配置**"。

    ![配置網路功能的狀態](./media/functions-create-vnet/Networking-1.png)

1. 在虛擬網路集成頁上，選擇 **"添加 VNet（預覽）"。**

    ![添加 VNet 集成預覽](./media/functions-create-vnet/networking-2.png)

1. 在 **"網路功能狀態**"中，使用圖像下方表中的設置：

    ![定義功能應用虛擬網路](./media/functions-create-vnet/networking-3.png)

    | 設定      | 建議的值  | 描述      |
    | ------------ | ---------------- | ---------------- |
    | **虛擬網路** | MyResourceGroup-vnet | 此虛擬網路是您之前創建的虛擬網路。 |
    | **子** | 創建新子網 | 在虛擬網路中創建一個子網，供函數應用使用。 必須將 VNet 集成配置為使用空子網。 函數使用與 VM 不同的子網並不重要。 虛擬網路自動路由兩個子網之間的流量。 |
    | **子網名稱** | 功能網 | 新子網路的名稱。 |
    | **虛擬網路位址塊** | 10.10.0.0/16 | 選擇 WordPress 網站使用的相同位址塊。 應只定義一個位址塊。 |
    | **位址範圍** | 10.10.2.0/24   | 子網大小限制高級計畫功能應用可以擴展到的實例總數。 此示例使用具有`/24`254 個可用主機位址的子網。 此子網預配過高，但易於計算。 |

1. 選擇 **"確定"** 以添加子網。 關閉 VNet 集成和網路功能狀態頁面以返回到功能應用頁面。

功能應用現在可以訪問運行 WordPress 網站的虛擬網路。 接下來，使用[Azure 函數代理](functions-proxies.md)從 WordPress 網站返回檔。

## <a name="create-a-proxy-to-access-vm-resources"></a>創建代理以訪問 VM 資源

啟用 VNet 集成後，您可以在函數應用中創建代理，將請求轉發到虛擬網路中運行的 VM。

1. 在函數應用中，選擇 **"代理** > **+**"，然後使用圖像下方表中的代理設置：

    ![定義代理設置](./media/functions-create-vnet/create-proxy.png)

    | 設定  | 建議的值  | 描述      |
    | -------- | ---------------- | ---------------- |
    | **名稱** | PlAnT | 名稱可以是任何值。 它用於標識代理。 |
    | **路由範本** | /工廠 | 映射到 VM 資源的路由。 |
    | **後端 URL** | HTTP：//<YOUR_VM_IP>/wp-內容/主題/21/資產/圖像/標題.jpg | 替換為`<YOUR_VM_IP>`之前創建的 WordPress VM 的 IP 位址。 此映射從網站返回單個檔。 |

1. 選擇 **"創建**"以將代理添加到函數應用。

## <a name="try-it-out"></a>試做

1. 在瀏覽器中，嘗試訪問用作後端 URL 的**URL。** 如預期的那樣，請求超時。超時的原因是您的 WordPress 網站僅連接到您的虛擬網路，而不是互聯網連接。

1. 從新代理複製**代理 URL**值並將其粘貼到瀏覽器的網址列中。 返回的圖像來自虛擬網路中運行的 WordPress 網站。

    ![從 WordPress 網站返回的工廠影像檔](./media/functions-create-vnet/plant.png)

您的功能應用已連接到互聯網和虛擬網路。 代理通過公共 Internet 接收請求，然後充當簡單的 HTTP 代理，將該請求轉發到連接的虛擬網路。 然後，代理通過互聯網公開將回應轉發給您。

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>後續步驟

在本教程中，WordPress 網站用作在函數應用中使用代理調用的 API。 此方案是一個很好的教程，因為它易於設置和視覺化。 您可以使用部署在虛擬網路中的任何其他 API。 您還可以創建一個函數，其代碼調用部署在虛擬網路中的 API。 更現實的情況是，使用資料用戶端 API 呼叫部署在虛擬網路中的 SQL Server 實例的函數。

在高級計畫中運行的功能與高級 V2 計畫中的 Web 應用共用相同的基礎應用服務基礎結構。 [Azure 應用服務中 Web 應用](../app-service/overview.md)的所有文檔都適用于高級計畫功能。

> [!div class="nextstepaction"]
> [瞭解有關函數中的網路選項的詳細資訊](./functions-networking-options.md)

[保費計畫]: functions-scale.md#premium-plan

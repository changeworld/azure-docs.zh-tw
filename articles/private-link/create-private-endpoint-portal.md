---
title: 快速入門 - 使用 Azure 入口網站建立私人端點
description: 使用本快速入門來了解如何使用 Azure 入口網站建立私人端點。
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 10/20/2020
ms.author: allensu
ms.openlocfilehash: 395912ae70c5a01bd7de9a80cf8a507dd516028e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018064"
---
# <a name="quickstart-create-a-private-endpoint-using-the-azure-portal"></a>快速入門：使用 Azure 入口網站建立私人端點

藉由使用私人端點來安全地連線到 Azure Web 應用程式，以開始使用 Azure Private Link。

在本快速入門中，您將建立 Azure Web 應用程式的私人端點，並部署虛擬機器來測試私人連線。  

您可以針對不同類型的 Azure 服務 (例如，Azure SQL 和 Azure 儲存體) 建立私人端點。

## <a name="prerequisites"></a>必要條件

* 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 在 Azure 訂用帳戶中部署具有 **PremiumV2 層** 或更高層級 App Service 方案的 Azure Web 應用程式。  
    * 如需詳細資訊和範例，請參閱[快速入門：在 Azure 中建立 ASP.NET Core Web 應用程式](../app-service/quickstart-dotnetcore.md)。 
    * 如需關於建立 Web 應用程式和端點的詳細教學課程，請參閱[教學課程：使用 Azure 私人端點連線至 Web 應用程式](tutorial-private-endpoint-webapp-portal.md)。

## <a name="sign-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 登入 Azure 入口網站。

## <a name="create-a-virtual-network-and-bastion-host"></a>建立虛擬網路和堡壘主機

在本節中，您會建立虛擬網路、子網路和堡壘主機。 

堡壘主機將用來安全地連線到虛擬機器，以測試私人端點。

1. 在畫面的左上方，選取 [建立資源] > [網路] > [虛擬網路]  ，或在搜尋方塊中搜尋 [虛擬網路]  。

2. 在 [建立虛擬網路] 中，在 [基本] 索引標籤中輸入或選取這項資訊：

    | **設定**          | **值**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **專案詳細資料**  |                                                                 |
    | 訂用帳戶     | 選取您的 Azure 訂用帳戶                                  |
    | 資源群組   | 選取 [CreatePrivateEndpointQS-rg] |
    | **執行個體詳細資料** |                                                                 |
    | 名稱             | 輸入 **myVNet**                                    |
    | 區域           | 選取 [西歐]。|

3. 選取 [IP 位址]  索引標籤，或選取頁面底部的 [下一步：  IP 位置] 按鈕。

4. 在 [IP 位址] 索引標籤中，輸入這項資訊：

    | 設定            | 值                      |
    |--------------------|----------------------------|
    | IPv4 位址空間 | 輸入 **10.1.0.0/16** |

5. 在 [子網路名稱] 下，選取 [預設] 字組。

6. 在 [編輯子網路] 中，輸入這項資訊：

    | 設定            | 值                      |
    |--------------------|----------------------------|
    | 子網路名稱 | 輸入 mySubnet |
    | 子網路位址範圍 | 輸入 **10.1.0.0/24** |

7. 選取 [儲存]。

8. 選取 [安全性] 索引標籤。

9. 在 [BastionHost] 底下，選取 [啟用]。 輸入此資訊：

    | 設定            | 值                      |
    |--------------------|----------------------------|
    | Bastion 名稱 | 輸入 **myBastionHost** |
    | AzureBastionSubnet 位址空間 | 輸入 **10.1.1.0/24** |
    | 公用 IP 位址 | 選取 [建立新的]。 </br> 在 [名稱] 中，輸入 myBastionIP。 </br> 選取 [確定]。 |


8. 選取 [檢閱 + 建立] 索引標籤，或選取 [檢閱 + 建立] 按鈕。

9. 選取 [建立]  。

## <a name="create-a-virtual-machine"></a>建立虛擬機器

在本節中，您將建立將用來測試私人端點的虛擬機器。

1. 在入口網站的左上角，選取 [建立資源] > [計算] > [虛擬機器] 或在搜尋方塊中搜尋 [虛擬機器]。
   
2. 在 [建立虛擬機器] 中，輸入或選取 [基本資訊] 索引標籤中的值：

    | 設定 | 值                                          |
    |-----------------------|----------------------------------|
    | **專案詳細資料** |  |
    | 訂用帳戶 | 選取您的 Azure 訂用帳戶 |
    | 資源群組 | 選取 [CreatePrivateEndpointQS-rg] |
    | **執行個體詳細資料** |  |
    | 虛擬機器名稱 | 輸入 myVM |
    | 區域 | 選取 [西歐]。 |
    | 可用性選項 | 選取 [不需要基礎結構備援] |
    | 映像 | 選取 [Windows Server 2019 Datacenter - Gen1] |
    | Azure Spot 執行個體 | 選取 [否] |
    | 大小 | 選擇 VM 大小或接受預設設定 |
    | **系統管理員帳戶** |  |
    | 使用者名稱 | 輸入使用者名稱 |
    | 密碼 | 輸入密碼 |
    | 確認密碼 | 再次輸入密碼 |

3. 選取 [網路] 索引標籤，或選取 **[下一步：磁碟]** ，然後選取 **[下一步：網路]** 。
  
4. 在 [網路功能] 索引標籤中，選取或輸入：

    | 設定 | 值 |
    |-|-|
    | **網路介面** |  |
    | 虛擬網路 | **myVNet** |
    | 子網路 | **mySubnet** |
    | 公用 IP | 選取 [無]。 |
    | NIC 網路安全性群組 | **基本**|
    | 公用輸入連接埠 | 選取 [無]。 |
   
5. 選取 [檢閱 + 建立]。 
  
6. 檢閱設定，然後選取 [建立]。

## <a name="create-a-private-endpoint"></a>建立私人端點

在本節中，您將為您在必要條件一節中建立的 Web 應用程式建立私人端點。

1. 在入口網站的畫面左上角，選取 [建立資源] > [網路] > [Private Link]，或在搜尋方塊中輸入 **Private Link**。

2. 選取 [建立]。

3. 在 **Private Link 中心** 內，選取左側功能表中的 [私人端點]。

4. 在 [私人端點] 中，選取 [+ 新增]。

5. 在 [建立私人端點] 的 [基本] 索引標籤中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳細資料** | |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 選取 [CreatePrivateEndpointQS-rg]。 您已在上一節中建立此資源群組。|
    | **執行個體詳細資料** |  |
    | 名稱  | 輸入 myPrivateEndpoint。 |
    | 區域 | 選取 [西歐]。 |

6. 選取 [資源] 索引標籤或 [下一步：資源] 按鈕 (位於頁面底部)。
    
7. 在 [資源] 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 連線方法 | 選取 [連線到我目錄中的 Azure 資源]。 |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源類型 | 選取 [Microsoft.Web/sites]。 |
    | 資源 | 選取 [\<your-web-app-name>]。 </br> 選取您在必要條件中所建立 Web 應用程式的名稱。 |
    | 目標子資源 | 選取 [網站]。 |

8. 選取 [設定] 索引標籤或 [下一步：設定] 按鈕 (位於畫面底部)。

9. 在 [設定] 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | **網路功能** |  |
    | 虛擬網路 | 選取 [myVNet]。 |
    | 子網路 | 選取 [mySubnet]。 |
    | **私人 DNS 整合** |  |
    | 與私人 DNS 區域整合 | 保留預設值 [是]。 |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 私人 DNS 區域 | 保留預設值 [(新增) privatelink.azurewebsites.net]。
    

13. 選取 [檢閱 + 建立]。

14. 選取 [建立]。

## <a name="test-connectivity-to-private-endpoint"></a>測試對私人端點的連線能力

在本節中，您將使用您在上一個步驟中建立的虛擬機器，連線到私人端點上的 Web 應用程式。

1. 在左側瀏覽窗格中，選取 [資源群組]。

2. 選取 [CreatePrivateEndpointQS-rg]。

3. 選取 [myVM]。

4. 在 **myVM** 的 [概觀] 頁面上，選取 [連線] 然後 [堡壘]。

5. 選取藍色 [使用堡壘] 按鈕。

6. 輸入您在虛擬機器建立期間輸入的使用者名稱和密碼。

7. 連線之後，在伺服器上開啟 Windows PowerShell。

8. 輸入 `nslookup <your-webapp-name>.azurewebsites.net`。 以您在上一個步驟中建立的 Web 應用程式名稱取代 **\<your-webapp-name>** 。  您會收到類似下面所顯示的訊息：

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.1.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    針對 Web 應用程式名稱，會傳回 **10.1.0.5** 的私人 IP 位址。  此位址位於您先前所建立虛擬網路的子網路中。

11. 在與 **myVM** 的堡壘連線中，開啟 Internet Explorer。

12. 輸入 Web 應用程式的 URL：**https://\<your-webapp-name>.azurewebsites.net**.。

13. 如果您尚未部署應用程式，則會收到預設的 Web 應用程式頁面：

    :::image type="content" source="./media/create-private-endpoint-portal/web-app-default-page.png" alt-text="預設 Web 應用程式頁面。" border="true":::

18. 關閉與 **myVM** 的連線。

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續使用此應用程式，請使用下列步驟刪除虛擬網路、虛擬機器和儲 Web 應用程式：

1. 從左側功能表，選取 [資源群組]。

2. 選取 [CreatePrivateEndpointQS-rg]。

3. 選取 [刪除資源群組]。

4. 在 [輸入資源群組名稱] 中，輸入 **CreatePrivateEndpointQS-rg**。

5. 選取 [刪除]。


## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立一個：

* 建立虛擬網路和堡壘主機。
* 虛擬機器。
* Azure Web 應用程式的私人端點。

您已使用虛擬機器在私人端點上安全地測試與 Web 應用程式的連線。



若要深入了解可支援私人端點的服務，請參閱：
> [!div class="nextstepaction"]
> [Private Link 可用性](private-link-overview.md#availability)

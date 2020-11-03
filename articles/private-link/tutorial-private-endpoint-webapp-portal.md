---
title: 教學課程：使用 Azure 私人端點連線至 Web 應用程式
titleSuffix: Azure Private Link
description: 本教學課程是使用 Azure 私人端點來私下連線到 Web 應用程式的入門課程。
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: tutorial
ms.date: 10/19/2020
ms.openlocfilehash: 502c48a92f5b41c4434d03139335a0ce05fa451f
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896968"
---
# <a name="tutorial-connect-to-a-web-app-using-an-azure-private-endpoint"></a>教學課程：使用 Azure 私人端點連線至 Web 應用程式

Azure 私人端點是 Azure 中私人連結的基本建置組塊。 其可讓 Azure 資源 (例如虛擬機器 (VM)) 與私人連結資源進行私密通訊。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立虛擬網路和堡壘主機。
> * 建立虛擬機器。
> * 建立 Web 應用程式。
> * 建立私人端點。
> * 測試對 Web 應用程式私人端點的連線能力。

如尚未擁有 Azure 訂用帳戶，請在開始之前先建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!Note]
> 私人端點可位在 PremiumV2 層、PremiumV3 層 Windows Web 應用程式、Linux Web 應用程式和 Azure Functions Premium 方案 (有時稱為 Elastic Premium 方案) 的公用區域。 

## <a name="prerequisites"></a>先決條件

* Azure 訂用帳戶

## <a name="sign-in-to-azure"></a>登入 Azure

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="create-a-virtual-network-and-bastion-host"></a>建立虛擬網路和堡壘主機

在本節中，您會建立虛擬網路、子網路和堡壘主機。 

堡壘主機將用來安全地連線到虛擬機器，以測試私人端點。

1. 在畫面的左上方，選取 [建立資源] > [網路] > [虛擬網路]  ，或在搜尋方塊中搜尋 [虛擬網路]  。

2. 在 [建立虛擬網路] 中，在 [基本] 索引標籤中輸入或選取這項資訊：

    | **設定**          | **值**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **專案詳細資料**  |                                                                 |
    | 訂用帳戶     | 選取您的 Azure 訂用帳戶                                  |
    | 資源群組   | 選取 **myResourceGroup** |
    | **執行個體詳細資料** |                                                                 |
    | 名稱             | 輸入 **myVNet**                                    |
    | 區域           | 選取 [西歐] |

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
    | 資源群組 | 選取 **myResourceGroup** |
    | **執行個體詳細資料** |  |
    | 虛擬機器名稱 | 輸入 myVM |
    | 區域 | 選取 [西歐] |
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

## <a name="create-web-app"></a>建立 Web 應用程式

在本節中，您將建立 Web 應用程式。

1. 在左側功能表中，選取 [建立資源] > [儲存體] > [Web 應用程式]，或在搜尋方塊中搜尋 [Web 應用程式]。

2. 在 [建立 Web 應用程式] 的 [基本] 索引標籤中，輸入或選取下列資訊：

    | 設定 | 值                                          |
    |-----------------------|----------------------------------|
    | **專案詳細資料** |  |
    | 訂用帳戶 | 選取您的 Azure 訂用帳戶 |
    | 資源群組 | 選取 **myResourceGroup** |
    | **執行個體詳細資料** |  |
    | 名稱 | 輸入 **mywebapp** 。 如果名稱無法使用，請輸入唯一的名稱。 |
    | 發佈 | 選取 [程式碼]。 |
    | 執行階段堆疊 | 選取 .NET Core 3.1 (LTS)。 |
    | 作業系統 | 選取 [Windows]。 |
    | 區域 | 選取 [西歐] |
    | **App Service 方案** |  |
    | Windows 方案 (西歐) | 選取 [建立新的]。 </br> 在 [名稱] 中輸入 **myServicePlan** 。 |
    | SKU 和大小 | 選取 [變更大小]。 </br> 在 [規格選擇器] 畫面中，選取 [P2V2]。 </br> 選取 [套用]  。 |
   
3. 選取 [檢閱 + 建立]。

4. 選取 [建立]。

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/create-web-app.png" alt-text="在 Azure 入口網站中建立 Web 應用程式的 [基本] 索引標籤。" border="true":::

## <a name="create-private-endpoint"></a>建立私人端點

1. 在左側功能表中，選取 [所有資源] > [mywebapp]，或您在建立期間選擇的名稱。

2. 在 Web 應用程式概觀中，選取 [設定] > [網路]。

3. 在 [網路] 中，選取 [設定私人端點連線]。

4. 在 [私人端點連線] 畫面中，選取 [+ 新增]。

5. 在 [新增私人端點] 中輸入或選取下列資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 名稱 | 輸入 **mywebappendpoint** 。 |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 虛擬網路 | 選取 [myVNet]。 |
    | 子網路 | 選取 [mySubnet]。 |
    | 與私人 DNS 區域整合 | 選取 [是]。 |

6. 選取 [確定]。
    

## <a name="test-connectivity-to-private-endpoint"></a>測試對私人端點的連線能力

在本節中，您將使用您在上一個步驟中建立的虛擬機器，連線到私人端點上的 Web 應用程式。

1. 在左側瀏覽窗格中，選取 [資源群組]。

2. 選取 **myResourceGroup** 。

3. 選取 [myVM]。

4. 在 **myVM** 的 [概觀] 頁面上，選取 [連線] 然後 [堡壘]。

5. 選取藍色 [使用堡壘] 按鈕。

6. 輸入您在虛擬機器建立期間輸入的使用者名稱和密碼。

7. 連線之後，在伺服器上開啟 Windows PowerShell。

8. 輸入 `nslookup <webapp-name>.azurewebsites.net`。 以您在上一個步驟中建立的 Web 應用程式名稱取代 **\<webapp-name>** 。  您會收到類似下面所顯示的訊息：

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.1.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    針對 Web 應用程式名稱，會傳回 **10.1.0.5** 的私人 IP 位址。  此位址位於您先前所建立虛擬網路的子網路中。

9. 在您的本機電腦上開啟網頁瀏覽器，並輸入 Web 應用程式的外部 URL： **https://\<webapp-name>.azurewebsites.net** 。

10. 確認您收到 **403** 頁面。 此頁面表示 Web 應用程式無法從外部存取。

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/web-app-ext-403.png" alt-text="外部 Web 應用程式位址的 403 頁面。" border="true":::

11. 在與 **myVM** 的堡壘連線中，開啟 Internet Explorer。

12. 輸入 Web 應用程式的 URL： **https://\<webapp-name>.azurewebsites.net**.。

13. 確認您收到預設的 Web 應用程式頁面。

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/web-app-default-page.png" alt-text="預設 Web 應用程式頁面。" border="true":::

18. 關閉與 **myVM** 的連線。

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續使用此應用程式，請使用下列步驟刪除虛擬網路、虛擬機器和儲 Web 應用程式：

1. 從左側功能表，選取 [資源群組]。

2. 選取 **myResourceGroup** 。

3. 選取 [刪除資源群組]。

4. 在 [輸入資源群組名稱] 中，輸入 **myResourceGroup** 。

5. 選取 [刪除]  。

## <a name="next-steps"></a>後續步驟

了解如何建立私人連結服務：
> [!div class="nextstepaction"]
> [建立 Private Link 服務](create-private-link-service-portal.md)

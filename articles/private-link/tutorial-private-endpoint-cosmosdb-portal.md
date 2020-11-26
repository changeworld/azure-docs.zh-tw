---
title: 教學課程：使用 Azure 私人端點連線至 Azure Cosmos 帳戶
titleSuffix: Azure Private Link
description: 本教學課程是使用 Azure 私人端點來私下連線到 Azure Cosmos 帳戶的入門課程。
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: tutorial
ms.date: 9/25/2020
ms.openlocfilehash: a544d0c5fafbdaf9d272fed552fb38eda613292f
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95522136"
---
# <a name="tutorial-connect-to-an-azure-cosmos-account-using-an-azure-private-endpoint"></a>教學課程：使用 Azure 私人端點連線至 Azure Cosmos 帳戶

Azure 私人端點是 Azure 中私人連結的基本建置組塊。 其可讓 Azure 資源 (例如虛擬機器 (VM)) 與私人連結資源進行私密通訊。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立虛擬網路和堡壘主機。
> * 建立虛擬機器。
> * 建立具有私人端點的 Cosmos DB 帳戶。
> * 測試對 Azure Cosmos DB 帳戶私人端點的連線能力。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必要條件

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
    | 區域           | 選取 [美國東部] |

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
    | 區域 | 選取 [美國東部] |
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

## <a name="create-a-cosmos-db-account-with-a-private-endpoint"></a>建立具有私人端點的 Cosmos DB 帳戶

在本節中，您會建立 Cosmos DB 帳戶，並設定私人端點。

1. 在左側功能表中，選取 [建立資源] > [資料庫] > [Cosmos DB 帳戶]，或在搜尋方塊中搜尋 [Cosmos DB 帳戶]。

2. 在 [建立 Cosmos DB 帳戶] 的 [基本] 索引標籤中，輸入或選取下列資訊：

    | 設定 | 值                                          |
    |-----------------------|----------------------------------|
    | **專案詳細資料** |  |
    | 訂用帳戶 | 選取 Azure 訂用帳戶。 |
    | 資源群組 | 選取 **myResourceGroup**。 |
    | **執行個體詳細資料** |  |
    | 帳戶名稱 | 輸入 **mycosmosdb**。 如果名稱無法使用，請輸入唯一的名稱。 |
    | API | 選取 [核心 (SQL)]。 |
    | Location | 選取 [美國東部]  。 |
    | 容量模式 | 保留預設值 [佈建的輸送量]。 |
    | 申請免費層折扣 | 保留預設值 [不要套用]。 |
    | 異地備援 | 保留預設值 [停用]。 |
    | 多區域寫入 | 保留預設值 [停用]。 |
   
3. 選取 [網路功能] 索引標籤，或選取 **[下一步：網路功能]** 按鈕。

4. 在 [網路] 索引標籤中，輸入或選取以下資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | **網路連線** | |
    | 連線方法 | 選取 [私人端點]。 |
    | **設定防火牆** | |
    | 允許從 Azure 入口網站存取 | 保留預設值 [允許]。 |
    | 允許從我的 IP 存取 | 保留預設值 [拒絕]。 |

5. 在 [私人端點] 中，選取 [+ 新增]。

6. 在 [建立私人端點] 中輸入或選取下列資訊：

    | 設定 | 值                                          |
    |-----------------------|----------------------------------|
    | 訂用帳戶 | 選取您的 Azure 訂用帳戶 |
    | 資源群組 | 選取 **myResourceGroup** |
    | Location | 選取 [美國東部] |
    | 名稱 | 輸入 myPrivateEndpoint |
    | 目標子資源 | 保留預設值 [Core (SQL)] |
    | **網路功能** |  |
    | 虛擬網路 | 選取 [myVNet] |
    | 子網路 | 選取 [mySubnet] |
    | **私人 DNS 整合** |
    | 與私人 DNS 區域整合 | 保留預設值 [是] |
    | 私人 DNS 區域 | 保留預設值：(新增) privatelink.documents.azure.com |

7. 選取 [確定]  。

8. 選取 [檢閱 + 建立]。

9. 選取 [建立]。

### <a name="add-a-database-and-a-container"></a>新增資料庫和容器

1. 選取 [移至資源]，或在 Azure 入口網站的左側功能表中，選取 [所有資源] > [mycosmosdb]。

2. 在左側功能表中，選取 [資料總管]。

3. 在 [資料總管] 視窗中，選取 [新增容器]。

4. 在 [新增容器] 中，輸入或選取下列資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 資料庫識別碼 | 保留預設值 [新建]。 </br> 在文字方塊中輸入 **mydatabaseid**。 |
    | 輸送量 (400 - 100,000 RU/秒) | 保留預設值 [手動]。 </br> 在文字方塊中輸入 **400**。 |
    | 容器識別碼 | 輸入 **mycontainerid** |
    | 資料分割索引鍵 | 輸入 **/mykey** |

5. 選取 [確定]  。

10. 在 CosmosDB 帳戶的 [設定] 區段中，選取 [金鑰]。

11. 選取 **myResourceGroup**。

12. 選取您在上一個步驟中建立的儲存體帳戶。

14. 選取並複製 **PRIMARY CONNECTION STRING**。

## <a name="test-connectivity-to-private-endpoint"></a>測試對私人端點的連線能力

在本節中，您將使用您在上一個步驟中建立的虛擬機器，連線到私人端點上的 Cosmos DB 帳戶。

1. 在左側瀏覽窗格中，選取 [資源群組]。

2. 選取 **myResourceGroup**。

3. 選取 [myVM]。

4. 在 **myVM** 的 [概觀] 頁面上，選取 [連線] 然後 [堡壘]。

5. 選取藍色 [使用堡壘] 按鈕。

6. 輸入您在虛擬機器建立期間輸入的使用者名稱和密碼。

7. 連線之後，在伺服器上開啟 Windows PowerShell。

8. 輸入 `nslookup <storage-account-name>.documents.azure.com`。 將 **\<storage-account-name>** 取代為您在先前步驟中建立的儲存體帳戶名稱。 

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mycosmosdb8675.privatelink.documents.azure.com
    Address:  10.1.0.5
    Aliases:  mycosmosdb8675.documents.azure.com
    ```

    會針對 Cosmos DB 帳戶名稱傳回 **10.1.0.5** 的私人 IP 位址。  此位址位於您先前所建立虛擬網路的子網路中。

9. 在虛擬機器上安裝 [Microsoft Azure 儲存體總管](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows&toc=%252fazure%252fstorage%252fblobs%252ftoc.json)。

10. 在安裝 [Microsoft Azure 儲存體總管] 之後，選取 [完成]。  保持方塊核取狀態以開啟應用程式。

11. 在 [連線到 Azure 儲存體] 畫面中，選取 [取消]。

12. 在儲存體總管中，以滑鼠右鍵選取 [Cosmos DB 帳戶]，然後選取 [連線到 Cosmos DB]。

13. 在 [選取 API] 底下，保留預設值 [SQL]。

14. 在 [連接字串] 底下的方塊中，貼上您在先前步驟中所複製 Cosmos DB 帳戶的連接字串。

15. 選取 [下一步] 。

16. 確認 [連線摘要] 中的設定是否正確。  

17. 選取 [連接]  。

18. 關閉與 **myVM** 的連線。


## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續使用此應用程式，請使用下列步驟刪除虛擬網路、虛擬機器和 Cosmos DB 帳戶：

1. 從左側功能表，選取 [資源群組]。

2. 選取 **myResourceGroup**。

3. 選取 [刪除資源群組]。

4. 在 [輸入資源群組名稱] 中，輸入 **myResourceGroup**。

5. 選取 [刪除]  。

## <a name="next-steps"></a>後續步驟

您已在本教學課程中建立：

* 建立虛擬網路和堡壘主機。
* 虛擬機器。
* Cosmos DB 帳戶。

了解如何建立私人連結服務：
> [!div class="nextstepaction"]
> [建立 Private Link 服務](create-private-link-service-portal.md)
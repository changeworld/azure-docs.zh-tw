---
title: 教學課程：使用 Azure 私人端點連線至儲存體帳戶
titleSuffix: Azure Private Link
description: 開始使用 Azure 私人端點以私下連線到儲存體帳戶。
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: tutorial
ms.date: 9/25/2020
ms.openlocfilehash: 69bee753c2134b6eebe9c5df0a554c965208ad7c
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91366205"
---
# <a name="tutorial-connect-to-a-storage-account-using-an-azure-private-endpoint"></a>教學課程：使用 Azure 私人端點連線至儲存體帳戶

Azure 私人端點是 Azure 中私人連結的基本建置組塊。 其可讓 Azure 資源 (例如虛擬機器 (VM)) 與私人連結資源進行私密通訊。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立虛擬網路和堡壘主機。
> * 建立虛擬機器。
> * 建立具有私人端點的儲存體帳戶。
> * 測試對儲存體帳戶私人端點的連線能力。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="create-a-virtual-network-and-bastion-host"></a>建立虛擬網路和堡壘主機

在本節中，您會建立虛擬網路、子網路和堡壘主機。 

堡壘主機將用來安全地連線到虛擬機器，以測試私人端點。

1. 在畫面的左上方，選取 [建立資源] > [網路] > [虛擬網路]  ，或在搜尋方塊中搜尋 [虛擬網路]  。

2. 在 [建立虛擬網路]  中，在 [基本]  索引標籤中輸入或選取這項資訊：

    | **設定**          | **ReplTest1**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **專案詳細資料**  |                                                                 |
    | 訂用帳戶     | 選取您的 Azure 訂用帳戶                                  |
    | 資源群組   | 選取 **myResourceGroup** |
    | **執行個體詳細資料** |                                                                 |
    | 名稱             | 輸入 **myVNet**                                    |
    | 區域           | 選取 [美國東部] |

3. 選取 [IP 位址]  索引標籤，或選取頁面底部的 [下一步：  IP 位置] 按鈕。

4. 在 [IP 位址]  索引標籤中，輸入這項資訊：

    | 設定            | 值                      |
    |--------------------|----------------------------|
    | IPv4 位址空間 | 輸入 **10.1.0.0/16** |

5. 在 [子網路名稱] 下，選取 [預設] 字組。

6. 在 [編輯子網路]  中，輸入這項資訊：

    | 設定            | 值                      |
    |--------------------|----------------------------|
    | 子網路名稱 | 輸入 mySubnet |
    | 子網路位址範圍 | 輸入 **10.1.0.0/24** |

7. 選取 [儲存]。

8. 選取 [安全性]**** 索引標籤。

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

## <a name="create-storage-account-with-a-private-endpoint"></a>建立具有私人端點的儲存體帳戶

在本節中，您會建立儲存體帳戶，並設定私人端點。

1. 在左側功能表中，選取 [建立資源] > [儲存體] > [儲存體帳戶]，或在搜尋方塊中搜尋 [儲存體帳戶]。

2. 在 [建立儲存體帳戶] 的 [基本] 索引標籤中，輸入或選取下列資訊：

    | 設定 | 值                                          |
    |-----------------------|----------------------------------|
    | **專案詳細資料** |  |
    | 訂用帳戶 | 選取您的 Azure 訂用帳戶 |
    | 資源群組 | 選取 **myResourceGroup** |
    | **執行個體詳細資料** |  |
    | 儲存體帳戶名稱 | 輸入 **mystorageaccount**。 如果名稱無法使用，請輸入唯一的名稱。 |
    | Location | 選取 [美國東部] |
    | 效能 | 保留預設值 [標準] |
    | 帳戶種類 | 保留預設值 [儲存體 (一般用途 v2)] |
    | 複寫| 保留預設 [讀取權限異地備援儲存體 (RA-GRS)] |
   
3. 選取 [網路功能] 索引標籤，或選取 **[下一步：網路功能]** 按鈕。

4. 在 [網路功能] 索引標籤的 [連線方法] 中，選取 [私人端點]。

5. 在 [私人端點] 中，選取 [+ 新增]。

6. 在 [建立私人端點] 中輸入或選取下列資訊：

    | 設定 | 值                                          |
    |-----------------------|----------------------------------|
    | 訂用帳戶 | 選取您的 Azure 訂用帳戶 |
    | 資源群組 | 選取 **myResourceGroup** |
    | Location | 選取 [美國東部] |
    | 名稱 | 輸入 myPrivateEndpoint |
    | 儲存體子資源 | 保留預設值 [blob] |
    | **網路功能** |  |
    | 虛擬網路 | 選取 [myVNet] |
    | 子網路 | 選取 [mySubnet] |
    | **私人 DNS 整合** |
    | 與私人 DNS 區域整合 | 保留預設值 [是] |
    | 私人 DNS 區域 | 保留預設值 (新增) privatelink.blob.core.windows.net |

7. 選取 [確定]。

8. 選取 [檢閱 + 建立]。

9. 選取 [建立]  。

10. 在左側瀏覽窗格中，選取 [資源群組]。

11. 選取 **myResourceGroup**。

12. 選取您在上一個步驟中建立的儲存體帳戶。

13. 在儲存體帳戶的 [設定] 區段中，選取 [存取金鑰]。

14. 針對 **key1** 的 [連接字串] 選取 [複製]。

## <a name="test-connectivity-to-private-endpoint"></a>測試對私人端點的連線能力

在本節中，您將使用您在上一個步驟中建立的虛擬機器，連線到私人端點上的儲存體帳戶。

1. 在左側瀏覽窗格中，選取 [資源群組]。

2. 選取 **myResourceGroup**。

3. 選取 [myVM]****。

4. 在 **myVM** 的 [概觀] 頁面上，選取 [連線] 然後 [堡壘]。

5. 選取藍色 [使用堡壘] 按鈕。

6. 輸入您在虛擬機器建立期間輸入的使用者名稱和密碼。

7. 連線之後，在伺服器上開啟 Windows PowerShell。

8. 輸入 `nslookup <storage-account-name>.blob.core.windows.net`。 將 **\<storage-account-name>** 取代為您在先前步驟中建立的儲存體帳戶名稱。  您會收到類似下面所顯示的訊息：

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mystorageaccount8675.privatelink.blob.core.windows.net
    Address:  10.1.0.5
    Aliases:  mystorageaccount8675.blob.core.windows.net
    ```

    會針對儲存體帳戶名稱傳回 **10.1.0.5** 的私人 IP 位址。  此位址位於您先前所建立虛擬網路的子網路中。

9. 在虛擬機器上安裝 [Microsoft Azure 儲存體總管](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows)。

10. 在安裝 [Microsoft Azure 儲存體總管] 之後，選取 [完成]。  保持方塊核取狀態以開啟應用程式。

11. 在 [連線到 Azure 儲存體] 畫面中，選取 [使用連接字串]。

12. 選取 [下一步] 。

13. 在 [顯示名稱] 中，輸入先前步驟中的儲存體帳戶名稱。

14. 在 [連接字串] 底下的方塊中，貼上您在先前步驟中所複製儲存體帳戶的連接字串。

15. 選取 [下一步] 。

16. 確認 [連線摘要] 中的設定是否正確。  

17. 選取 [連接]  。

18. 關閉與 **myVM** 的連線。

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續使用此應用程式，請使用下列步驟刪除虛擬網路、虛擬機器和儲存體帳戶：

1. 從左側功能表，選取 [資源群組]。

2. 選取 **myResourceGroup**。

3. 選取 [刪除資源群組]。

4. 在 [輸入資源群組名稱] 中，輸入 **myResourceGroup**。

5. 選取 [刪除]  。

## <a name="next-steps"></a>後續步驟

了解如何建立私人連結服務：
> [!div class="nextstepaction"]
> [建立 Private Link 服務](create-private-link-service-portal.md)

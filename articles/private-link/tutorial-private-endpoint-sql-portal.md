---
title: 教學課程 - 使用 Azure 私人端點連線到 Azure SQL 伺服器 - 入口網站
description: 使用此教學課程來了解如何使用 Azure 入口網站建立具有私人端點的 Azure SQL 伺服器。
services: private-link
author: asudbring
ms.service: private-link
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: allensu
ms.openlocfilehash: def14cec9d010104876acaf9588560722dd98884
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2020
ms.locfileid: "93145662"
---
# <a name="tutorial---connect-to-an-azure-sql-server-using-an-azure-private-endpoint---azure-portal"></a>教學課程 - 使用 Azure 私人端點連線到 Azure SQL 伺服器 - Azure 入口網站

Azure 私人端點是 Azure 中私人連結的基本建置組塊。 其可讓 Azure 資源 (例如虛擬機器 (VM)) 與私人連結資源進行私密通訊。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立虛擬網路和堡壘主機。
> * 建立虛擬機器。
> * 建立 Azure SQL 伺服器和私人端點。
> * 測試 SQL 伺服器私人端點的連線能力。

## <a name="prerequisites"></a>必要條件

* 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

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
    | 資源群組   | 選取 **CreateSQLEndpointTutorial-rg** |
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
    | 資源群組 | 選取 **CreateSQLEndpointTutorial** |
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

## <a name="create-an-azure-sql-server-and-private-endpoint"></a><a name ="create-a-private-endpoint"></a>建立 Azure SQL 伺服器和私人端點

在本節中，您將在 Azure 中建立 SQL 伺服器。 

1. 在 Azure 入口網站的畫面左上角，選取 [建立資源] > [資料庫] > [SQL 資料庫]。

1. 在 [建立SQL 資料庫] 的 [基本] 索引標籤中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳細資料** | |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 選取 **CreateSQLEndpointTutorial**。 您已在上一節中建立此資源群組。|
    | **資料庫詳細資料** |  |
    | 資料庫名稱  | 輸入 **mysqldatabase**。 如果此名稱已被使用，請建立唯一名稱。 |
    | 伺服器 | 選取 [建立新的]。 |

6. 在 [新增伺服器] 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 伺服器名稱  | 輸入 mysqlserver。 如果此名稱已被使用，請建立唯一名稱。|
    | 伺服器管理員登入 | 輸入您所選擇的系統管理員名稱。 |
    | 密碼 | 輸入您選擇的密碼。 密碼長度至少必須有 8 個字元，而且符合定義的需求。 |
    | Location | 選取 [美國東部] |
    
7. 選取 [確定]。

8. 選取 [網路功能] 索引標籤，或選取 **[下一步：網路功能]** 按鈕。

9. 在 [網路] 索引標籤中，輸入或選取此資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | **網路連線** | |
    | 連線方法 | 選取 [私人端點]。 |
   
10. 在 [私人端點] 中選取 [+ 新增私人端點]。

11. 在 [建立私人端點] 中輸入或選取此資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 選取 **CreateSQLEndpointTutorial**。 |
    | Location | 選取 [美國東部]  。 |
    | Name | 輸入 **myPrivateSQLendpoint**。 |
    | 目標子資源 | 選取 [SQLServer]。 |
    | **網路功能** |  |
    | 虛擬網路 | 選取 [myVNet]。 |
    | 子網路 | 選取 [mySubnet]。 |
    | **私人 DNS 整合** | |
    | 與私人 DNS 區域整合 | 保留預設值 [是]。 |
    | 私人 DNS 區域 | 保留預設值 [(新增) privatelink.database.windows.net]。 |

12. 選取 [確定]。 

13. 選取 [檢閱 + 建立]。

14. 選取 [建立]。

## <a name="test-connectivity-to-private-endpoint"></a>測試對私人端點的連線能力

在本節中，您將使用您在上一個步驟中建立的虛擬機器，連線到私人端點上的 SQL 伺服器。

1. 在左側瀏覽窗格中，選取 [資源群組]。

2. 選取 **CreateSQLEndpointTutorial**。

3. 選取 [myVM]。

4. 在 **myVM** 的 [概觀] 頁面上，選取 [連線] 然後 [堡壘]。

5. 選取藍色 [使用堡壘] 按鈕。

6. 輸入您在虛擬機器建立期間輸入的使用者名稱和密碼。

7. 連線之後，在伺服器上開啟 Windows PowerShell。

8. 輸入 `nslookup <sqlserver-name>.database.windows.net`。 將 **\<sqlserver-name>** 取代為您在先前步驟中建立的 SQL 伺服器名稱。  您會收到類似下面所顯示的訊息：

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mysqlserver8675.privatelink.database.windows.net
    Address:  10.1.0.5
    Aliases:  mysqlserver8675.database.windows.net
    ```

    會針對 SQL 伺服器名稱傳回 **10.1.0.5** 的私人 IP 位址。  此位址位於您先前所建立虛擬網路的子網路中。


9. 在 **myVM** 上安裝 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017&preserve-view=true)。

10. 開啟 [SQL Server Management Studio]  。

4. 在 [連線至伺服器] 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 伺服器類型 | 選取 [資料庫引擎]。|
    | 伺服器名稱 | 輸入 **\<sqlserver-name>.database.windows.net** |
    | 驗證 | 選取 [SQL Server 驗證]。 |
    | 使用者名稱 | 輸入您在伺服器建立期間輸入的使用者名稱 |
    | 密碼 | 輸入您在建立伺服器時輸入的密碼 |
    | 記住密碼 | 選取 [是]。 |

1. 選取 [連接]。
2. 瀏覽左側功能表中的資料庫。
3. (選擇性) 從 **mysqldatabase** 建立或查詢資訊。
4. 關閉對 **myVM** 的遠端桌面連線。 

## <a name="clean-up-resources"></a>清除資源 
在私人端點、SQL Server 和 VM 使用完畢後，請刪除資源群組及其包含的所有資源： 
1. 在入口網站頂端的 **搜尋** 方塊中，輸入 **CreateSQLEndpointTutorial**，然後從搜尋結果中選取 [CreateSQLEndpointTutorial]。 
2. 選取 [刪除資源群組]。 
3. 針對 [輸入資源群組名稱] 輸入 CreateSQLEndpointTutorial，然後選取 [刪除]。

## <a name="next-steps"></a>後續步驟

您已在本教學課程中建立：

* 建立虛擬網路和堡壘主機。
* 虛擬機器。
* 具有私人端點的 Azure SQL 伺服器。

您已使用虛擬機器在私人端點上安全地測試與 SQL 伺服器的連線。

了解如何建立私人連結服務：
> [!div class="nextstepaction"]
> [建立 Private Link 服務](create-private-link-service-portal.md)

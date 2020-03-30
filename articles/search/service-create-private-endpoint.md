---
title: 為安全連線創建專用終結點
titleSuffix: Azure Cognitive Search
description: 在虛擬網路中設置專用終結點，以便安全連線到 Azure 認知搜索服務
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 2664b1abd4131cf1dca186c7b044e338bf1efa84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75945830"
---
# <a name="create-a-private-endpoint-for-a-secure-connection-to-azure-cognitive-search-preview"></a>為與 Azure 認知搜索的安全連線創建專用終結點（預覽）

在本文中，使用門戶創建新的 Azure 認知搜索服務實例，該實例無法通過公共 IP 位址訪問。 接下來，在同一虛擬網路中配置 Azure 虛擬機器，並使用它通過專用終結點訪問搜索服務。

> [!Important]
> Azure 認知搜索的專用終結點支援可[應請求](https://aka.ms/SearchPrivateLinkRequestAccess)作為受限訪問預覽提供。 預覽功能在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 
>
> 獲得預覽存取權限後，可以使用 Azure 門戶或[管理 REST API 版本 2019-10-06-預覽](https://docs.microsoft.com/rest/api/searchmanagement/)為服務配置專用終結點。
>   

## <a name="why-use-private-endpoint-for-secure-access"></a>為什麼要使用專用終結點進行安全訪問？

Azure 認知搜索[的專用終結點](../private-link/private-endpoint-overview.md)允許虛擬網路上的用戶端通過[專用連結](../private-link/private-link-overview.md)安全地訪問搜索索引中的資料。 專用終結點使用[虛擬網路位址空間](../virtual-network/virtual-network-ip-addresses-overview-arm.md#private-ip-addresses)中的 IP 位址進行搜索服務。 用戶端和搜索服務之間的網路流量通過虛擬網路和 Microsoft 骨幹網路上的專用鏈路遍歷，消除了公共 Internet 的暴露。 有關支援專用連結的其他 PaaS 服務的清單，請查看產品文檔中[的可用性部分](../private-link/private-link-overview.md#availability)。

搜索服務的專用終結點使您能夠：

- 阻止搜索服務的公共終結點上的所有連接。
- 通過阻止資料從虛擬網路進行外泄，提高虛擬網路的安全性。
- 使用[VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)或[快速路由](../expressroute/expressroute-locations.md)與專用對等互連連接到虛擬網路的本地網路安全地連接到搜索服務。

> [!NOTE]
> 預覽版當前存在一些限制，您應該注意：
> * 僅適用于**基本**層上的搜索服務。 
> * 在美國西部 2、美國中西部、美國東部、美國中南部、澳大利亞東部和澳大利亞東南部地區提供。
> * 當服務終結點是私有的時，某些門戶功能將禁用。 您可以查看和管理服務等級資訊，但出於安全原因，對索引資料和服務中各種元件（如索引、索引子和技能集定義）的門戶訪問受到限制。
> * 當服務終結點是私有的時，必須使用[搜索 REST API](https://docs.microsoft.com/rest/api/searchservice/)將文檔上載到索引。
> * 您必須使用以下連結才能在 Azure 門戶中查看專用終結點支援選項：https://portal.azure.com/?feature.enablePrivateEndpoints=true



## <a name="request-access"></a>要求存取 

按一下[請求存取權限](https://aka.ms/SearchPrivateLinkRequestAccess)以註冊此預覽功能。 該表單請求有關您、您的公司和一般網路拓撲的資訊。 一旦我們查看您的請求，您將收到一封包含其他說明的確認電子郵件。

## <a name="create-the-virtual-network"></a>建立虛擬網路

在本節中，您將創建一個虛擬網路和子網來承載將用於訪問搜索服務的專用終結點的 VM。

1. 在 Azure 門戶主頁選項卡中，選擇 **"創建資源** > **網路** > **虛擬網路**"。

1. 在 [建立虛擬網路]**** 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 名稱 | 輸入*我的虛擬網路* |
    | 位址空間 | 輸入*10.1.0.0/16* |
    | 訂用帳戶 | 選取您的訂用帳戶|
    | 資源群組 | 選擇 **"創建新**"，輸入*我的資源組*，然後選擇 **"確定"** |
    | Location | 選擇**美國西部**或您正在使用的任何區域|
    | 子網路 - 名稱 | 輸入*我的子網* |
    | 子網路 - 位址範圍 | 輸入*10.1.0.0/24* |
    |||

1. 將其他項目保留為預設值，然後選取 [建立]****。


## <a name="create-a-search-service-with-a-private-endpoint"></a>使用專用終結點創建搜索服務

在本節中，您將使用專用終結點創建新的 Azure 認知搜索服務。 

1. 在 Azure 門戶中螢幕的左上角，選擇 **"創建資源** > **Web** > **Azure 認知搜索**"。

1. 在新**搜索服務 - 基礎知識中**，輸入或選擇此資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳情** | |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 選擇**我的資源組**。 您已在上一節中建立此項目。|
    | **實例詳細資訊** |  |
    | URL | 輸入唯一名稱。 |
    | Location | 選擇請求訪問此預覽功能時指定的區域。 |
    | 定價層 | 選擇 **"更改定價層**"並選擇 **"基本**"。 預覽需要此層。 |
    |||
  
1. 選擇**下一個：縮放**。

1. 將值保留為預設值，然後選擇 **"下一步：網路**"。

1. 在 **"新搜索服務 - 網路"中**，選擇 **"專用**"以進行**端點連接（資料）。**

1. 在 **"新搜索服務 - 網路**"中，選擇 "**專用終結點**"下**添加**。 

1. 在 **"創建專用終結點**"中，輸入或選擇此資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 選擇**我的資源組**。 您已在上一節中建立此項目。|
    | Location | 選取 [美國西部]****。|
    | 名稱 | 輸入 *我的私人終結點*。  |
    | 目標子資源 | 離開預設**搜索服務**。 |
    | **網路** |  |
    | 虛擬網路  | 從資源組 *"我的資源組"* 中選擇 *我的虛擬網路*。 |
    | 子網路 | 選取 [mySubnet] **。 |
    | **私人 DNS 整合** |  |
    | 與私人 DNS 區域整合  | 保留預設**的"是**"。 |
    | 私人 DNS 區域  | 保留預設值 [ （新） privatelink.search.windows.net]。 |
    |||

1. 選取 [確定]****。 

1. 選擇 **"審閱" = 創建**。 您會移至 [檢閱 + 建立]**** 頁面，其中 Azure 會驗證您的設定。 

1. 當您看到**驗證傳遞**的消息時，選擇 **"創建**"。 

1. 完成新服務的預配後，流覽到您剛剛創建的資源。

1. 從左側內容功能表中選擇 **"鍵**"。

1. 在連接到服務時，請複製**主管理金鑰**，以便以後使用。

## <a name="create-a-virtual-machine"></a>建立虛擬機器

1. 在 Azure 門戶中螢幕的左上角，選擇 **"創建資源** > **計算** > **虛擬機器**"。

1. 在 [建立虛擬機器 - 基本]**** 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳情** | |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 選擇**我的資源組**。 您已在上一節中建立此項目。  |
    | **實例詳細資訊** |  |
    | 虛擬機器名稱 | 輸入 myVm**。 |
    | 區域 | 選擇**美國西部**或您正在使用的任何區域。 |
    | 可用性選項 | 保留預設值 [不需要基礎結構備援]****。 |
    | 映像 | 選取 [Windows Server 2019 Datacenter]****。 |
    | 大小 | 保留預設值 [標準 DS1 v2]****。 |
    | **管理員帳戶** |  |
    | 使用者名稱 | 輸入您選擇的使用者名稱。 |
    | 密碼 | 輸入您選擇的密碼。 密碼長度至少必須有 12 個字元，而且符合[定義的複雜度需求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。|
    | 確認密碼 | 再次輸入密碼。 |
    | **入站連接埠規則** |  |
    | 公用輸入連接埠 | 保留預設 **"允許所選埠**"。 |
    | 選取輸入連接埠 | 保留預設**RDP （3389）。** |
    | **節省費用** |  |
    | 已經有 Windows 授權？ | 保留預設值 [否]****。 |
    |||

1. 選擇 **"下一步"：磁片**。

1. 在 **"創建虛擬機器磁片"中**，保留預設值並選擇 **"下一步：網路**"。

1. 在 [建立虛擬機器 - 網路]**** 中，選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 虛擬網路 | 保留預設**的"我的虛擬網路**"。  |
    | 位址空間 | 保留預設值 [10.1.0.0/24]****。|
    | 子網路 | 保留預設值 [mySubnet (10.1.0.0/24)]****。|
    | 公用 IP | 保留預設值 [(new) myVm-ip]****。 |
    | 公用輸入連接埠 | 選取 [允許選取的連接埠]****。 |
    | 選取輸入連接埠 | 選取 [HTTP]**** 和 [RDP]****。|
    ||

1. 選擇 **"審閱" = 創建**。 您會移至 [檢閱 + 建立]**** 頁面，其中 Azure 會驗證您的設定。

1. 當您看到**驗證傳遞**的消息時，選擇 **"創建**"。 


## <a name="connect-to-the-vm"></a>連接至 VM

下載，然後連接到 VM *myVm，* 如下所示：

1. 在入口網站的搜尋列中，輸入 myVm**。

1. 選取 [連線] **** 按鈕。 選取 [連線]**** 按鈕之後，隨即會開啟 [連線至虛擬機器]****。

1. 選擇**下載 RDP 檔**。 Azure 會建立一個「遠端桌面通訊協定」(*.rdp*) 檔案，並下載至您的電腦。

1. 開啟下載的 .rdp* 檔案。

    1. 如果出現提示，請選取 [連接]****。

    1. 輸入您在建立 VM 時指定的使用者名稱和密碼。

        > [!NOTE]
        > 您可能需要選擇**更多選項** > **"使用其他帳戶**"來指定創建 VM 時輸入的憑據。

1. 選取 [確定]****。

1. 您可能會在登入過程中收到憑證警告。 如果您收到憑證警告，請選取 [是]**** 或 [繼續]****。

1. 當 VM 桌面出現之後，將它最小化以回到您的本機桌面。  


## <a name="test-connections"></a>測試連接

在本節中，您將驗證對搜索服務的私人網路絡訪問，並私下連接到專用終結點。

回想一下，與搜索服務的所有交互都需要搜索 REST [API](https://docs.microsoft.com/rest/api/searchservice/)。 此預覽不支援門戶和 .NET SDK。

1. 在 myVM ** 的遠端桌面中，開啟 PowerShell。

1. 輸入"nsup_搜索服務名稱"*search.windows.net'

    您將收到如下訊息：
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    [search service name].privatelink.search.windows.net
    Address:  10.0.0.5
    Aliases:  [search service name].search.windows.net
    ```

1. 從 VM 連接到搜索服務並創建索引。 您可以按照此[快速入門](search-get-started-postman.md)在 Postman 中使用 REST API 在服務中創建新的搜索索引。 設置來自 Postman 的請求需要搜索服務終結點（HTTPs：//[搜索服務名稱]search.windows.net）和您在上一步中複製的管理員 api 鍵。

1. 從 VM 完成快速入門是您確認服務已完全正常運行。

1. 關閉遠端桌面連線到 *myVM。* 

1. 要驗證服務在公共終結點上不可訪問，請打開本地工作站上的 Postman，並嘗試快速入門中的頭幾個任務。 如果收到遠端伺服器不存在的錯誤，則已成功為搜索服務配置專用終結點。

## <a name="clean-up-resources"></a>清除資源 
使用專用終結點、搜索服務和 VM 完成後，請刪除資源組及其包含的所有資源：
1. 在門戶頂部的 **"搜索"** 框中輸入 *"我的資源組* "，然後從搜尋結果中選擇 *"我的資源組* "。 
1. 選取 [刪除資源群組]****。 
1. 輸入 *"資源組*  **"以鍵入資源組名稱**，然後選擇 **"刪除**"。

## <a name="next-steps"></a>後續步驟
在本文中，您在虛擬網路和具有專用終結點的搜索服務上創建了 VM。 您從 Internet 連接到 VM，並使用專用連結安全地連接到搜索服務。 要瞭解有關私有終結點的更多內容，請參閱 [什麼是 Azure 專用終結點？](../private-link/private-endpoint-overview.md)

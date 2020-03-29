---
title: 使用 Azure 專用終結點私下連接到 Web 應用
description: 使用 Azure 專用終結點私下連接到 Web 應用
author: ericgre
ms.assetid: b8c5c7f8-5e90-440e-bc50-38c990ca9f14
ms.topic: article
ms.date: 03/12/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 2f10c7378ae7681b14df6e96b6a6f1adac832d1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80287810"
---
# <a name="connect-privately-to-a-web-app-using-azure-private-endpoint-preview"></a>使用 Azure 專用終結點（預覽）私下連接到 Web 應用

Azure 專用終結點是 Azure 中專用連結的基本構建基塊。 它允許您私下連接到 Web 應用。
在此快速入門中，您將學習如何部署具有專用終結點的 Web 應用，以及如何從虛擬機器連接到此 Web 應用。

有關詳細資訊，請參閱為[Azure Web 應用使用專用終結點][privatenedpointwebapp]。

> [!Note]
>預覽版在美國東部和美國西部 2 區提供，適用于所有高級 V2 Windows 和 Linux Web 應用。 

## <a name="sign-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 登入 Azure 入口網站。

## <a name="virtual-network-and-virtual-machine"></a>虛擬網路和虛擬機器

在本節中，您將創建虛擬網路和子網來承載用於通過專用終結點訪問 Web 應用的 VM。

### <a name="create-the-virtual-network"></a>建立虛擬網路

在本節中，您將創建一個虛擬網路和子網。

1. 在螢幕的左上角，在搜索框中選擇 **"創建資源** > **網路** > **虛擬網路**"或"搜索**虛擬網路**"。

1. 在 **"創建虛擬網路"** 中，在"基本"選項卡中輸入或選擇此資訊：

   > [!div class="mx-imgBorder"]
   > ![創建虛擬網路][1]

1. 按一下 **"下一步：IP 位址>"** 並輸入或選擇此資訊：

   > [!div class="mx-imgBorder"]
   >![配置 IP 位址][2]

1. 在子網部分中，按一下 **"+ 添加子網"** 並輸入以下資訊，然後按一下 **"添加"**

   > [!div class="mx-imgBorder"]
   >![添加子網][3]

1. 按一下 **"查看 + 創建"**

1. 驗證通過後，按一下 **"創建"**

### <a name="create-virtual-machine"></a>建立虛擬機器

1. 在 Azure 門戶中螢幕的左上角，選擇 **"創建資源** > **計算** > **虛擬機器"**

1. 在 [建立虛擬機器 - 基本] 中，輸入或選取這項資訊：

   > [!div class="mx-imgBorder"]
   >![虛擬機器基本][4]

1. 選擇 **"下一步：磁片"**

   保留預設設置。

1. 選擇 **"下一步：網路"，** 選擇此資訊：

   > [!div class="mx-imgBorder"]
   >![網路功能][5]

1. 按一下 **"查看 + 創建"**

1. 驗證傳遞消息時，按一下 **"創建"**

## <a name="create-your-web-app-and-private-endpoint"></a>創建 Web 應用和專用終結點

在本節中，您將使用專用終結點創建專用 Web 應用。

> [!Note]
>專用終結點功能僅適用于高級 V2 SKU。

### <a name="web-app"></a>Web 應用程式

1. 在 Azure 門戶中螢幕的左上角，選擇 **"創建資源** > **Web** > **Web 應用**"

1. 在"創建 Web 應用 - 基礎知識"中，輸入或選擇此資訊：

   > [!div class="mx-imgBorder"]
   >![Web 應用程式基本][6]

1. 選擇 **"查看 + 創建"**

1. 驗證傳遞消息時，按一下 **"創建"**

### <a name="create-the-private-endpoint"></a>創建專用終結點

1. 在 Web 應用屬性中，選擇 **"設置** > **網路"，** 然後按一下 **"配置專用終結點連接"**

   > [!div class="mx-imgBorder"]
   >![網路應用網路][7]

1. 在嚮導中，按一下 **"+ 添加"**

   > [!div class="mx-imgBorder"]
   >![Web 應用專用終結點][8]

1. 填寫訂閱、VNet 和子網資訊，然後按一下 **"確定"**

   > [!div class="mx-imgBorder"]
   >![網路應用網路][9]

1. 查看私有終結點的創建

   > [!div class="mx-imgBorder"]
   >![查看][10]
   >![專用終結點的最終視圖][11]

## <a name="connect-to-a-vm-from-the-internet"></a>從網際網路連線至 VM

1. 在門戶的搜索欄中，輸入**myVm**
1. 選擇"**連接"按鈕**。 選擇"連接"按鈕後，打開"連接到虛擬機器"，選擇**RDP**

   > [!div class="mx-imgBorder"]
   >![RDP 按鈕][12]

1. Azure 創建遠端桌面協定 （.rdp） 檔，並在按一下 **"下載 RDP 檔**"後將其下載到電腦

   > [!div class="mx-imgBorder"]
   >![下載 RDP 檔][13]

1. 開啟 downloaded.rdp 檔案。

- 如果出現提示，請選取 [連接]。
- 輸入您在建立 VM 時指定的使用者名稱和密碼。

> [!Note]
> 您可能需要選擇更多選項>使用其他帳戶，以指定創建 VM 時輸入的憑據。

- 選取 [確定]。

1. 您可能會在登入過程中收到憑證警告。 如果您收到憑證警告，請選取 [是] 或 [繼續]。

1. 當 VM 桌面出現之後，將它最小化以回到您的本機桌面。

## <a name="access-web-app-privately-from-the-vm"></a>從 VM 私下訪問 Web 應用

在本節中，您將使用專用終結點私下連接到 Web 應用。

1. 獲取專用終結點的專用 IP，在搜索欄中鍵入**專用連結**，然後選擇專用連結

   > [!div class="mx-imgBorder"]
   >![私人連結][14]

1. 在專用連結中心中，選擇 **"專用終結點"** 以列出所有專用終結點

   > [!div class="mx-imgBorder"]
   >![專用連結中心][15]

1. 選擇指向 Web 應用和子網的專用終結點連結

   > [!div class="mx-imgBorder"]
   >![專用終結點屬性][16]

1. 複製您的專用終結點的專用 IP 和 Web 應用的 FQDN，在我們webappdemope.azurewebsites.net 10.10.2.4

1. 在 myVM 中，驗證 Web 應用是否無法通過公共 IP 訪問。 打開瀏覽器並粘貼 Web 應用名稱，您必須有一個 403 禁止錯誤頁面

   > [!div class="mx-imgBorder"]
   >![禁止][17]

> [!Important]
> 由於此功能處於預覽狀態，因此您需要手動管理 DNS 條目。

1. 創建主機條目，打開檔資源管理器並找到主機檔

   > [!div class="mx-imgBorder"]
   >![主機檔][18]

1. 使用記事本編輯主機檔，添加帶有私人 IP 位址和 Web 應用公共名稱的條目

   > [!div class="mx-imgBorder"]
   >![主機內容][19]

1. 儲存檔案

1. 打開瀏覽器並鍵入 Web 應用的 URL

   > [!div class="mx-imgBorder"]
   >![帶 PE 的網站][20]

1. 您正在通過專用終結點訪問 Web 應用

## <a name="clean-up-resources"></a>清除資源

使用專用終結點、Web 應用和 VM 完成後，請刪除資源組及其包含的所有資源：

1. 在門戶頂部的"搜索"框中輸入就緒-rg，並從搜尋結果中選擇就緒 rg。
1. 選取 [刪除資源群組]。
1. 輸入"鍵入資源組名稱"的就緒點，然後選擇"刪除"。

## <a name="next-steps"></a>後續步驟

在此快速入門中，您可以在虛擬網路、Web 應用和專用終結點上創建了 VM。 您從 Internet 連接到 VM，並使用專用連結安全地連接到 Web 應用。 要瞭解有關私有終結點的更多內容，請參閱[什麼是 Azure 專用終結點][privateendpoint]。

<!--Image references-->
[1]: ./media/create-private-endpoint-webapp-portal/createnetwork.png
[2]: ./media/create-private-endpoint-webapp-portal/ipaddresses.png
[3]: ./media/create-private-endpoint-webapp-portal/subnet.png
[4]: ./media/create-private-endpoint-webapp-portal/virtualmachine.png
[5]: ./media/create-private-endpoint-webapp-portal/vmnetwork.png
[6]: ./media/create-private-endpoint-webapp-portal/webapp.png
[7]: ./media/create-private-endpoint-webapp-portal/webappnetworking.png
[8]: ./media/create-private-endpoint-webapp-portal/webapppe.png
[9]: ./media/create-private-endpoint-webapp-portal/webapppenetwork.png
[10]: ./media/create-private-endpoint-webapp-portal/inprogress.png
[11]: ./media/create-private-endpoint-webapp-portal/webapppefinal.png
[12]: ./media/create-private-endpoint-webapp-portal/rdp.png
[13]: ./media/create-private-endpoint-webapp-portal/rdpdownload.png
[14]: ./media/create-private-endpoint-webapp-portal/pl.png
[15]: ./media/create-private-endpoint-webapp-portal/plcenter.png
[16]: ./media/create-private-endpoint-webapp-portal/privateendpointproperties.png
[17]: ./media/create-private-endpoint-webapp-portal/forbidden.png
[18]: ./media/create-private-endpoint-webapp-portal/explorer.png
[19]: ./media/create-private-endpoint-webapp-portal/hosts.png
[20]: ./media/create-private-endpoint-webapp-portal/webappwithpe.png

<!--Links-->
[privatenedpointwebapp]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
[privateendpoint]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview

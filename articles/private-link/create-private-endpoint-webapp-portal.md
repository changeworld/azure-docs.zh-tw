---
title: 使用 Azure 私人端點私下連線至 Web 應用程式
description: 使用 Azure 私人端點私下連線至 Web 應用程式
author: ericgre
ms.assetid: b8c5c7f8-5e90-440e-bc50-38c990ca9f14
ms.topic: how-to
ms.date: 06/02/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 1b3ac4c79ce92f591e74821a9f355717e4b22ea4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84737388"
---
# <a name="connect-privately-to-a-web-app-using-azure-private-endpoint-preview"></a>使用 Azure 私用端點（預覽）私下連線到 Web 應用程式

Azure 私用端點是 Azure 中私人連結的基本建立區塊。 它可讓您私下連接到 Web 應用程式。
在本快速入門中，您將瞭解如何部署具有私用端點的 Web 應用程式，並從虛擬機器連接到此 Web 應用程式。

如需詳細資訊，請參閱[使用 Azure Web 應用程式的私用端點][privatenedpointwebapp]。

> [!Note]
>預覽適用于 PremiumV2 Windows 和 Linux Web Apps 和彈性 Premium 功能的公用區域。 

## <a name="sign-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 登入 Azure 入口網站。

## <a name="virtual-network-and-virtual-machine"></a>虛擬網路和虛擬機器

在本節中，您將建立虛擬網路和子網，以裝載用來透過私人端點存取 Web 應用程式的 VM。

### <a name="create-the-virtual-network"></a>建立虛擬網路

在本節中，您將建立虛擬網路和子網。

1. 在畫面的左上方，選取 [**建立資源**]  >  [**網路**]  >  [**虛擬網路**]，或在搜尋方塊中搜尋 [**虛擬網路**]。

1. 在 [**建立虛擬網路**] 中，于 [基本] 索引標籤中輸入或選取這項資訊：

   > [!div class="mx-imgBorder"]
   > ![建立虛擬網路][1]

1. 按一下 [**下一步： IP 位址 >]** ，然後輸入或選取下列資訊：

   > [!div class="mx-imgBorder"]
   >![設定 IP 位址][2]

1. 在 [子網] 區段中，按一下 [ **+ 新增子網]** 並輸入下列資訊，然後按一下 [**新增]**

   > [!div class="mx-imgBorder"]
   >![新增子網][3]

1. 按一下 **[審查 + 建立]**

1. 通過驗證之後，按一下 [**建立]**

### <a name="create-virtual-machine"></a>建立虛擬機器

1. 在 [Azure 入口網站] 畫面的左上方，選取 [**建立資源**] [計算] [  >  **Compute**  >  **虛擬機器**]

1. 在 [建立虛擬機器 - 基本] 中，輸入或選取這項資訊：

   > [!div class="mx-imgBorder"]
   >![虛擬機器基本][4]

1. 選取 [**下一步：磁片]**

   保留預設設定。

1. 選取 **[下一步：網路]**，選取此資訊：

   > [!div class="mx-imgBorder"]
   >![網路功能][5]

1. 按一下 **[審查 + 建立]**

1. 通過驗證的訊息時，按一下 **[建立]**

## <a name="create-your-web-app-and-private-endpoint"></a>建立您的 Web 應用程式和私人端點

在本節中，您將使用私人端點來建立私用 Web 應用程式。

> [!Note]
>私人端點功能僅適用于 Premium V2 SKU。

### <a name="web-app"></a>Web 應用程式

1. 在 [Azure 入口網站] 畫面的左上方，選取 [**建立資源**] [  >  **web**  >  **應用程式**]

1. 在 [建立 Web 應用程式-基本] 中，輸入或選取這項資訊：

   > [!div class="mx-imgBorder"]
   >![Web 應用程式基本][6]

1. 選取 **[審查 + 建立]**

1. 通過驗證的訊息時，按一下 **[建立]**

### <a name="create-the-private-endpoint"></a>建立私用端點

1. 在 Web 應用程式屬性中，選取 [**設定**]  >  [**網路**]，然後按一下 **[設定您的私人端點連線]**

   > [!div class="mx-imgBorder"]
   >![Web 應用程式網路功能][7]

1. 在嚮導中，按一下 **[+ 新增]**

   > [!div class="mx-imgBorder"]
   >![Web 應用程式私人端點][8]

1. 填入訂用帳戶、VNet 和子網資訊，然後按一下 [**確定**]

   > [!div class="mx-imgBorder"]
   >![Web 應用程式網路功能][9]

1. 檢查私用端點的建立

   > [!div class="mx-imgBorder"]
   >![審查 ][10]
   > ![ 私用端點的最終觀點][11]

## <a name="connect-to-a-vm-from-the-internet"></a>從網際網路連線至 VM

1. 在入口網站的搜尋列中，輸入**myVm**
1. 選取 [連線 **] 按鈕**。 選取 [連線] 按鈕之後，[連線至虛擬機器] 隨即開啟，請選取 [ **RDP** ]

   > [!div class="mx-imgBorder"]
   >![RDP 按鈕][12]

1. 當您按一下 [**下載 rdp**檔案] 之後，Azure 會建立遠端桌面通訊協定（.rdp）檔案，並將它下載至您的電腦

   > [!div class="mx-imgBorder"]
   >![下載 RDP 檔案][13]

1. 開啟下載的 .rdp 檔案。

- 如果出現提示，請選取 [連接]。
- 輸入您在建立 VM 時指定的使用者名稱和密碼。

> [!Note]
> 您可能需要選取 [更多選擇] > [使用不同的帳戶]，以指定您在建立 VM 時輸入的認證。

- 選取 [確定]。

1. 您可能會在登入過程中收到憑證警告。 如果您收到憑證警告，請選取 [是] 或 [繼續]。

1. 當 VM 桌面出現之後，將它最小化以回到您的本機桌面。

## <a name="access-web-app-privately-from-the-vm"></a>從 VM 私下存取 Web 應用程式

在本節中，您會使用私用端點，私下連接到 Web 應用程式。

1. 取得私人端點的私人 IP，在 [搜尋] 列中輸入 [**私人連結**]，然後選取 [私人連結]

   > [!div class="mx-imgBorder"]
   >![私人連結][14]

1. 在私人連結中心，選取 [**私人端點**] 以列出您所有的私人端點

   > [!div class="mx-imgBorder"]
   >![私人連結中心][15]

1. 選取 Web 應用程式和子網的私人端點連結

   > [!div class="mx-imgBorder"]
   >![私用端點屬性][16]

1. 複製私人端點的私人 IP 和 Web 應用程式的 FQDN，在我們的案例中為 webappdemope.azurewebsites.net 10.10.2。4

1. 在 myVM 中，確認 Web 應用程式無法透過公用 IP 存取。 開啟瀏覽器並貼上 Web 應用程式名稱，您必須有403禁止的錯誤頁面

   > [!div class="mx-imgBorder"]
   >![禁止][17]

> [!Important]
> 因為這項功能處於預覽狀態，所以您必須手動管理 DNS 專案。

1. 建立主項目目，開啟檔案瀏覽器並尋找 hosts 檔案

   > [!div class="mx-imgBorder"]
   >![Hosts 檔案][18]

1. 使用 [記事本] 編輯 hosts 檔案，以新增具有私人 IP 位址和 Web 應用程式公用名稱的專案

   > [!div class="mx-imgBorder"]
   >![主控內容][19]

1. 儲存檔案

1. 開啟瀏覽器並輸入 web 應用程式的 url

   > [!div class="mx-imgBorder"]
   >![使用 PE 的網站][20]

1. 您要透過私人端點存取您的 Web 應用程式

## <a name="clean-up-resources"></a>清除資源

當您使用私用端點、Web 應用程式和 VM 完成時，請刪除資源群組及其包含的所有資源：

1. 在入口網站頂端的搜尋方塊中輸入 ready-rg，然後從搜尋結果中選取 [就緒-rg]。
1. 選取 [刪除資源群組]。
1. 針對 [類型] 資源組名輸入 ready-rg，然後選取 [刪除]。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已在虛擬網路、Web 應用程式和私人端點上建立 VM。 您已從網際網路連線至 VM，並使用私人連結安全地與 Web 應用程式通訊。 若要深入瞭解私用端點，請參閱[什麼是 Azure 私人端點][privateendpoint]。

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

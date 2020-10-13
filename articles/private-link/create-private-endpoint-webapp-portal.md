---
title: 使用 Azure 私人端點私下連接至 web 應用程式
description: 本文說明如何使用 Azure 私人端點私下連接至 web 應用程式。
author: ericgre
ms.assetid: b8c5c7f8-5e90-440e-bc50-38c990ca9f14
ms.topic: how-to
ms.date: 10/07/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 3e0f05240aba9b5c92689315e409aaabe793b3f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91772833"
---
# <a name="connect-privately-to-a-web-app-by-using-azure-private-endpoint"></a>使用 Azure 私人端點私下連接至 web 應用程式

Azure 私人端點是 Azure Private Link 的基本組建區塊。 使用私人端點時，您可以私下連線至 web 應用程式。 在本文中，您將瞭解如何使用私人端點部署 web 應用程式，然後從 (VM) 的虛擬機器連線至 web 應用程式。

如需詳細資訊，請參閱 [使用 Azure web 應用程式的私人端點][privateendpointwebapp]。

> [!Note]
> 私人端點適用于 >premiumv2 層、PremiumV3 層 Windows web 應用程式、Linux web 應用程式，以及 Azure Functions Premium 方案， (有時也稱為彈性 Premium 方案) 。 

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

開始之前，請先登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="create-a-virtual-network-and-virtual-machine"></a>建立虛擬網路和虛擬機器

在本節中，您會建立虛擬網路和子網，以裝載您將用來透過私人端點存取 web 應用程式的 VM。

### <a name="create-the-virtual-network"></a>建立虛擬網路

若要建立虛擬網路和子網，請執行下列動作：

1. 在左窗格中，選取 [**建立資源**  >  **網路**  >  **虛擬網路**]。

1. 在 [ **建立虛擬網路** ] 窗格中，選取 [ **基本** ] 索引標籤，然後輸入此處顯示的資訊：

   > [!div class="mx-imgBorder"]
   > ![Azure 入口網站中 [建立虛擬網路] 窗格的螢幕擷取畫面。][1]

1. 選取 [ **IP 位址** ] 索引標籤，然後輸入此處顯示的資訊：

   > [!div class="mx-imgBorder"]
   > ![[建立虛擬網路] 窗格上 [IP 位址] 索引標籤的螢幕擷取畫面。][2]

1. 在 [ **子網** ] 區段中，選取 [ **新增子網**]，輸入此處顯示的資訊，然後選取 [ **新增**]。

   > [!div class="mx-imgBorder"]
   > ![[新增子網] 窗格的螢幕擷取畫面。][3]

1. 選取 [檢閱 + 建立]****。

1. 驗證成功之後，請選取 [ **建立**]。

### <a name="create-the-virtual-machine"></a>建立虛擬機器

若要建立虛擬機器，請執行下列動作：

1. 在 [Azure 入口網站] 的左窗格中，選取 [**建立資源**  >  **計算**  >  **虛擬機器**]。

1. 在 [ **建立虛擬機器-基本** ] 窗格中，輸入此處顯示的資訊：

   > [!div class="mx-imgBorder"]
   > ![[建立虛擬機器] 窗格的螢幕擷取畫面。][4]

1. 完成時，選取 [下一步:  磁碟]。

1. 在 [ **磁片** ] 窗格中，保留預設設定，然後選取 **[下一步：網路]**。

1. 在 [ **網路** ] 窗格中，輸入此處顯示的資訊：

   > [!div class="mx-imgBorder"]
   > ![[建立虛擬機器] 窗格上 [網路] 索引標籤的螢幕擷取畫面。][5]

1. 選取 [檢閱 + 建立]****。

1. 驗證成功之後，請選取 [ **建立**]。

## <a name="create-a-web-app-and-a-private-endpoint"></a>建立 web 應用程式和私人端點

在本節中，您會建立使用私用端點的私用 web 應用程式。

> [!Note]
> 私人端點功能僅適用于 >premiumv2 和 PremiumV3 層。

### <a name="create-the-web-app"></a>建立 Web 應用程式

1. 在 [Azure 入口網站] 的左窗格中，選取 [**建立資源**  >  **web**  >  **應用程式**]。

1. 在 [ **Web 應用程式** ] 窗格中，選取 [ **基本** ] 索引標籤，然後輸入此處顯示的資訊：

   > [!div class="mx-imgBorder"]
   > ![[Web 應用程式] 窗格中 [基本] 索引標籤的螢幕擷取畫面。][6]

1. 選取 [檢閱 + 建立]****。

1. 驗證成功之後，請選取 [ **建立**]。

### <a name="create-the-private-endpoint"></a>建立私人端點

1. 在 [web 應用程式內容] 的 [ **設定**] 底下，選取 [ **網路**]，然後在 [私人端點連線] 下，選取 [ **設定您的私人端點連接**]。

   > [!div class="mx-imgBorder"]
   > ![Web 應用程式網路功能窗格上 [設定您的私人端點連線] 連結的螢幕擷取畫面。][7]

1. 在 [ **私人端點連接** ] 嚮導中，選取 [ **新增**]。

   > [!div class="mx-imgBorder"]
   > ![[私人端點連線] 中 [新增] 按鈕的螢幕擷取畫面。][8]

1. 在 [ **訂**用帳戶]、[ **虛擬網路**] 和 [ **子網** ] 下拉式清單中選取正確的資訊，然後選取 **[確定]**。

   > [!div class="mx-imgBorder"]
   > ![[新增私人端點] 窗格的螢幕擷取畫面。][9]

1. 監視私人端點建立的進度。

   > [!div class="mx-imgBorder"]
   > ![新增私人端點進度的螢幕擷取畫面。 ][10]
   >  ![新建立私人端點的螢幕擷取畫面。][11]

## <a name="connect-to-the-vm-from-the-internet"></a>從網際網路連接至 VM

1. 在 Azure 入口網站 **搜尋** 方塊中，輸入 **myVm**。
1. 選取 **[連線]**，然後選取 [ **RDP**]。

   > [!div class="mx-imgBorder"]
   > ![[MyVM] 窗格上 [RDP] 按鈕的螢幕擷取畫面。][12]

1. 在 [ **使用 RDP** 連線] 窗格中，選取 [ **下載 rdp**檔案]。  

   > [!div class="mx-imgBorder"]
   > ![[使用 RDP 連線] 窗格上 [下載 RDP 檔案] 按鈕的螢幕擷取畫面。][13]

   Azure 會建立遠端桌面通訊協定 (RDP) 檔，並將其下載到您的電腦。   

1. 開啟所下載的 RDP 檔案。

   a. 在提示中選取 **[連接]**。  
   b. 輸入您在建立 VM 時所指定的使用者名稱和密碼。

     > [!Note]
     > 若要使用這些認證，您可能需要選取**更多選項**，請  >  **使用不同的帳戶**。

1. 選取 [確定]  。

   > [!Note]
   > 如果您在登入過程中收到憑證警告，請選取 **[是]** 或 [ **繼續**]。

1. 當 VM 桌面視窗出現時，請將它最小化，以返回您的本機桌面。

## <a name="access-the-web-app-privately-from-the-vm"></a>從 VM 私下存取 web 應用程式

在本節中，您會使用私人端點私下連接至 web 應用程式。

1. 若要取得私人端點的私人 IP，請在 [ **搜尋** ] 方塊中輸入 **private link** ，然後在結果清單中，選取 [ **Private Link**]。

   > [!div class="mx-imgBorder"]
   > ![搜尋結果清單中 [Private Link] 連結的螢幕擷取畫面。][14]

1. 在 Private Link 中心的左窗格上，選取 [ **私人端點** ] 以顯示您的私人端點。

   > [!div class="mx-imgBorder"]
   > ![Private Link 中心內 [私人端點] 清單的螢幕擷取畫面。][15]

1. 選取連結至您的 web 應用程式和子網的私人端點。

   > [!div class="mx-imgBorder"]
   > ![私人端點的 [屬性] 窗格螢幕擷取畫面。][16]

1. 複製私人端點的私人 IP，以及 web 應用程式 (FQDN) 的完整功能變數名稱。 在上述範例中，私用識別碼是 *`webappdemope.azurewebsites.net 10.10.2.4`* 。

1. 在 [ **myVM** ] 窗格中，確認 web 應用程式無法透過公用 IP 存取。 若要這樣做，請開啟瀏覽器並貼上 web 應用程式名稱。 頁面應該會顯示「錯誤 403-禁止」訊息。

   > [!div class="mx-imgBorder"]
   > ![[錯誤 403-禁止] 錯誤頁面的螢幕擷取畫面。][17]

   針對 DNS，請執行下列其中一項：
 
   - 使用 Azure DNS 私用區域服務。  

     a. 建立名為的 DNS 私人區域 *`privatelink.azurewebsites.net`* ，然後將它連結至虛擬網路。  
     b. 建立兩個 A 記錄 (也就是應用程式名稱和服務控制管理員 [SCM] 名稱) 具有私人端點的 IP 位址。  
     > [!div class="mx-imgBorder"]
     > ![DNS 私人區域記錄的螢幕擷取畫面。][21]  

   - 使用 VM 的 *hosts* 檔案。  

     a. 建立主項目目、開啟檔案總管，然後尋找 *主機* 檔案。  
     > [!div class="mx-imgBorder"]
     > ![顯示檔案總管中 hosts 檔案的螢幕擷取畫面。][18]  
     b. 在文字編輯器中編輯 *主機* 檔案，以新增包含 web 應用程式之私人 IP 位址和公用名稱的專案。  
     > [!div class="mx-imgBorder"]
     > ![主機檔案文字的螢幕擷取畫面。][19]  
     c. 儲存檔案。

1. 在瀏覽器中，輸入 web 應用程式的 URL。

   > [!div class="mx-imgBorder"]
   > ![顯示 web 應用程式的瀏覽器螢幕擷取畫面。][20]

您現在已透過私人端點存取您的 web 應用程式。

## <a name="clean-up-resources"></a>清除資源

當您完成使用私人端點、web 應用程式和 VM 時，請刪除資源群組及其包含的所有資源。

1. 在 [Azure 入口網站] 的 [ **搜尋** ] 方塊中，輸入 **ready**，然後在結果清單中選取 [ **就緒-rg** ]。

1. 選取 [刪除資源群組]。

1. 在 **[輸入資源組名**] 底下，輸入 [ **就緒-rg**]，然後選取 [ **刪除**]。

## <a name="next-steps"></a>後續步驟

在本文中，您已在虛擬網路、web 應用程式和私人端點上建立 VM。 您已從網際網路連線到 VM，並使用 Private Link 安全地傳達給 web 應用程式。 

若要深入瞭解私人端點，請參閱 [什麼是 Azure 私人端點？][privateendpoint]。

<!--Image references-->
[1]: ./media/create-private-endpoint-webapp-portal/createnetwork.png
[2]: ./media/create-private-endpoint-webapp-portal/ipaddresses.png
[3]: ./media/create-private-endpoint-webapp-portal/subnet.png
[4]: ./media/create-private-endpoint-webapp-portal/virtual-machine.png
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
[21]: ./media/create-private-endpoint-webapp-portal/dns-private-zone-records.png


<!--Links-->
[privateendpointwebapp]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
[privateendpoint]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview

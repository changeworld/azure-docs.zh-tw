---
title: 教學課程：建立別名記錄以支援網域頂點名稱 - 流量管理員
titleSuffix: Azure DNS
description: 本教學課程說明如何使用「流量管理員」來設定 Auzre DNS 別名記錄，以支援使用網域頂點名稱。
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: rohink
ms.openlocfilehash: 749e5eae64aa0d33c90ef8694da9a093647b8a8b
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2020
ms.locfileid: "76937931"
---
# <a name="tutorial-configure-an-alias-record-to-support-apex-domain-names-with-traffic-manager"></a>教學課程：使用流量管理員設定別名記錄以支援頂點網域名稱 

您可以為網域名稱頂點建立別名記錄，以參考 Azure 流量管理員設定檔。 例如 contoso.com。 您可以不使用重新導向服務，而是設定讓 Azure DNS 直接從您的區域參考流量管理員設定檔。 


在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立主機 VM 和網路基礎結構。
> * 建立流量管理員設定檔。
> * 建立別名記錄。
> * 測試別名記錄。


如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>Prerequisites
必須提供可用的網域名稱，如此才能裝載於 Azure DNS 進行測試。 您必須擁有此網域的完整控制權。 完整控制權包括為網域設定名稱伺服器 (NS) 記錄的能力。

如需如何在 Azure DNS 中裝載網域的相關指示，請參閱[教學課程：在 Azure DNS 上託管您的網域](dns-delegate-domain-azure-dns.md)。

本教學課程使用的範例網域是 contoso.com，但請使用您自己的網域名稱。

## <a name="create-the-network-infrastructure"></a>建立網路基礎結構
首先，請建立一個可供放置 Web 伺服器的虛擬網路和子網域。
1. 在 https://portal.azure.com 登入 Azure 入口網站。
2. 在入口網站的左上方，選取 [建立資源]  。 在搜尋方塊中輸入資源群組  ，然後建立名為 **RG-DNS-Alias-TM** 的資源群組。
3. 選取 [建立資源]   > [網路]   > [虛擬網路]  。
4. 建立名為 **VNet-Servers** 的虛擬網路。 將它放在 **RG-DNS-Alias-TM** 資源群組中，然後將子網路命名為 **SN-Web**。

## <a name="create-two-web-server-virtual-machines"></a>建立兩部 Web 伺服器虛擬機器
1. 選取 [建立資源]   > [Windows Server 2016 VM]  。
2. 輸入 **Web-01** 作為名稱，然後將 VM 放在 [RG-DNS-Alias-TM]  資源群組中。 輸入使用者名稱和密碼，然後選取 [確定]  。
3. 針對 [大小]  ，選取具有 8 GB RAM 的 SKU。
4. 針對 [設定]  ，選取 [VNet-Servers]  虛擬網路和 [SN-Web]  子網路。
5. 選取 [公用 IP 位址]  。 在 [指派]  下方選取 [靜態]  ，然後選取 [確定]  。
6. 針對公用輸入連接埠，選取 [HTTP]   > [HTTPS]   > [RDP (3389)]  ，然後選取 [確定]  。
7. 在 [摘要]  頁面上，選取 [建立]  。 完成此程序需要數分鐘的時間。

重複此程序來建立名為 **Web-02** 的另一部虛擬機器。

### <a name="add-a-dns-label"></a>新增 DNS 標籤
公用 IP 位址需要有 DNS 標籤，才能與流量管理員搭配運作。
1. 在 [RG-DNS-Alias-TM]  資源群組中，選取 [Web-01-ip]  公用 IP 位址。
2. 在 [設定]  下方，選取 [設定]  。
3. 在 DNS 名稱標籤文字方塊中，輸入 **web01pip**。
4. 選取 [儲存]  。

針對 **Web-02-ip** 公用 IP 位址，使用 **web02pip** 作為 DNS 名稱標籤來重複此程序。

### <a name="install-iis"></a>安裝 IIS

在 **Web-01** 和 **Web-02** 上安裝 IIS。

1. 連線至 **Web-01** 並登入。
2. 在 [伺服器管理員]  儀表板上，選取 [新增角色及功能]  。
3. 選取 [下一步]  三次。 在 [伺服器角色]  頁面上，選取 [Web 伺服器 (IIS)]  。
4. 選取 [新增功能]  ，然後選取 [下一步]  。
5. 選取 [下一步]  四次。 然後，選取 [安裝]  。 完成此程序需要數分鐘的時間。
6. 在安裝完成後，選取 [關閉]  。
7. 請開啟網頁瀏覽器。 瀏覽至 **localhost**，以確認預設的 IIS 網頁已出現。

重複此程序，在 **Web-02**上安裝 IIS。


## <a name="create-a-traffic-manager-profile"></a>建立流量管理員設定檔

1. 開啟 [RG-DNS-Alias-TM]  資源群組，然後選取 [Web-01-ip]  公用 IP 位址。 記下 IP 位址以供稍後使用。 針對 **Web-02-ip** 公用 IP 位址重複此步驟。
1. 選取 [建立資源]   > [網路]   > [流量管理員設定檔]  。
2. 針對名稱輸入 TM-alias-test  。 將它放在 **RG-DNS-Alias-TM** 資源群組中。
3. 選取 [建立]  。
4. 在部署完成後，選取 [前往資源]  。
5. 在流量管理員設定檔頁面上的 [設定]  下方，選取 [端點]  。
6. 選取 [新增]  。
7. 針對 [類型]  選取 [外部端點]  ，並輸入 **EP-Web01** 作為 [名稱]  。
8. 在 [完整網域名稱 (FQDN) 或 IP]  文字方塊中，輸入您先前記下的 **Web-01-ip** IP 位址。
9. 選取與其他資源相同的 [位置]  ，然後選取 [確定]  。

使用您先前記下的 **Web-02-ip** IP 位址重複此程序，以新增 **Web-02** 端點。

## <a name="create-an-alias-record"></a>建立別名記錄

建立一個指向「流量管理員」設定檔的別名記錄。

1. 選取您的 Azure DNS 區域以開啟該區域。
2. 選取 [記錄集]  。
3. 將 [名稱]  文字方塊保留為空白，以代表網域名稱頂點。 例如 contoso.com。
4. 將 [類型]  保留為 [A]  記錄。
5. 選取 [別名記錄集]  核取方塊。
6. 選取 [選擇 Azure 服務]  ，然後選取 [TM-alias-test]  流量管理員設定檔。

## <a name="test-the-alias-record"></a>測試別名記錄

1. 從網頁瀏覽器中，瀏覽至您的網域名稱頂點。 例如 contoso.com。 您會看見 IIS 預設網頁。 關閉網頁瀏覽器。
2. 關閉 **Web-01** 虛擬機器。 請等候幾分鐘讓它完全關閉。
3. 開啟一個新的網頁瀏覽器，然後再次瀏覽至您的網域名稱頂點。
4. 您會再次看到 IIS 預設網頁，因為流量管理員已處理情況，並將流量導向至 **Web-02**。

## <a name="clean-up-resources"></a>清除資源

不再需要為本教學課程建立的資源群組時，請刪除 **RG-DNS-Alias-TM** 資源群組。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立一個別名記錄以使用頂點網域名稱來參考流量管理員設定檔。 若要了解 Azure DNS 和 Web 應用程式，請繼續進行 Web 應用程式的教學課程。

> [!div class="nextstepaction"]
> [位於區域頂點的主機負載平衡 Web 應用程式](./dns-alias-appservice.md)

---
title: 教學課程：建立 Azure DNS 別名記錄來參考 Azure 公用 IP 位址
description: 本教學課程說明如何設定 Azure DNS 別名記錄來參考 Azure 公用 IP 位址。
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: rohink
ms.openlocfilehash: d3017d09e94040d16950598dad360fe32930c16b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "80985434"
---
# <a name="tutorial-configure-an-alias-record-to-refer-to-an-azure-public-ip-address"></a>教學課程：設定別名記錄以參照 Azure 公用 IP 位址 

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立網路基礎結構。
> * 使用公用 IP 建立 Web 伺服器虛擬機器。
> * 建立一個指向公用 IP 的別名記錄。
> * 測試別名記錄。


如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>Prerequisites
必須提供可用的網域名稱，如此才能裝載於 Azure DNS 進行測試。 您必須擁有此網域的完整控制權。 完整控制權包括為網域設定名稱伺服器 (NS) 記錄的能力。

如需在 Azure DNS 中裝載網域的相關指示，請參閱[教學課程：在 Azure DNS 上託管您的網域](dns-delegate-domain-azure-dns.md)。

本教學課程使用的範例網域是 contoso.com，但請使用您自己的網域名稱。

## <a name="create-the-network-infrastructure"></a>建立網路基礎結構
首先，請建立一個可供放置 Web 伺服器的虛擬網路和子網域。
1. 登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。
2. 在入口網站的左上方，選取 [建立資源]  。 在搜尋方塊中輸入資源群組  ，然後建立名為 **RG-DNS-Alias-pip** 的資源群組。
3. 選取 [建立資源]   > [網路]   > [虛擬網路]  。
4. 建立名為 **VNet-Server** 的虛擬網路。 將它放在 **RG-DNS-Alias-pip** 資源群組中，然後將子網路命名為 **SN-Web**。

## <a name="create-a-web-server-virtual-machine"></a>建立 Web 伺服器虛擬機器
1. 選取 [建立資源]   > [Windows Server 2016 VM]  。
2. 輸入 **Web-01** 作為名稱，然後將 VM 放在 [RG-DNS-Alias-TM]  資源群組中。 輸入使用者名稱和密碼，然後選取 [確定]  。
3. 針對 [大小]  ，選取具有 8 GB RAM 的 SKU。
4. 針對 [設定]  ，選取 [VNet-Servers]  虛擬網路和 [SN-Web]  子網路。 針對公用輸入連接埠，選取 [HTTP]   > [HTTPS]   > [RDP (3389)]  ，然後選取 [確定]  。
5. 在 [摘要]  頁面上，選取 [建立]  。

完成此程序需要數分鐘的時間。 虛擬機器將會有一個連結的 NIC，其會有一個名為 Web-01-ip 的基本動態公用 IP。 每次將虛擬機器重新開機時，公用 IP 就會變更。

### <a name="install-iis"></a>安裝 IIS

在 **Web-01** 上安裝 IIS。

1. 連線至 **Web-01** 並登入。
2. 在 [伺服器管理員]  儀表板上，選取 [新增角色及功能]  。
3. 選取 [下一步]  三次。 在 [伺服器角色]  頁面上，選取 [Web 伺服器 (IIS)]  。
4. 選取 [新增功能]  ，然後選取 [下一步]  。
5. 選取 [新增功能]  ，然後選取 [下一步]  。 完成此程序需要數分鐘的時間。
6. 在安裝完成後，選取 [關閉]  。
7. 請開啟網頁瀏覽器。 瀏覽至 **localhost**，以確認預設的 IIS 網頁已出現。

## <a name="create-an-alias-record"></a>建立別名記錄

建立一個指向公用 IP 位址的別名記錄。

1. 選取您的 Azure DNS 區域以開啟該區域。
2. 選取 [記錄集]  。
3. 在 [名稱]  文字方塊中，選取 **web01**。
4. 將 [類型]  保留為 [A]  記錄。
5. 選取 [別名記錄集]  核取方塊。
6. 選取 [選擇 Azure 服務]  ，然後選取 [Web-01-ip]  公用 IP 位址。

## <a name="test-the-alias-record"></a>測試別名記錄

1. 在 [RG-DNS-Alias-pip]  資源群組中，選取 [Web-01]  虛擬機器。 記下公用 IP 位址。
1. 從網頁瀏覽器中，瀏覽至 Web01-01 虛擬機器的完整網域名稱。 例如 **web01.contoso.com**。 您現在會看到 IIS 預設網頁。
2. 關閉網頁瀏覽器。
3. 停止 **Web-01** 虛擬機器，然後將它重新啟動。
4. 在虛擬機器重新啟動後，記下虛擬機器的新公用 IP 位址。
5. 開啟新的瀏覽器。 再次瀏覽至 Web01-01 虛擬機器的完整網域名稱。 例如 **web01.contoso.com**。

此程序會成功，因為您使用別名記錄來指向公用 IP 位址資源，而不是標準的 A 記錄。

## <a name="clean-up-resources"></a>清除資源

不再需要為本教學課程建立的資源群組時，請刪除 **RG-DNS-Alias-pip** 資源群組。


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立別名記錄來參考 Azure 公用 IP 位址。 若要了解 Azure DNS 和 Web 應用程式，請繼續進行 Web 應用程式的教學課程。

> [!div class="nextstepaction"]
> [在自訂網域中建立 Web 應用程式的 DNS 記錄](./dns-web-sites-custom-domain.md)

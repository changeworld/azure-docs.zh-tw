---
title: 在雲端服務中設定自訂網域名稱 | Microsoft Docs
description: 了解如何藉由 DNS 設定，公開您的 Azure 應用程式或資料到自訂網域的網際網路上。  這些範例使用 Azure 入口網站。
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: tagore
ms.openlocfilehash: e764e6a474b9843d43f9e8af9cf3b6a8ddf37189
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811641"
---
# <a name="configuring-a-custom-domain-name-for-an-azure-cloud-service"></a>設定 Azure 雲端服務的自訂網域名稱
當您建立雲端服務時，Azure 會將它指派給 **cloudapp.net**的子網域。 例如，如果您的雲端服務的名稱為 "contoso"，您的使用者可以透過 URL (如 `http://contoso.cloudapp.net`) 存取應用程式。 Azure 也會指派虛擬 IP 位址。

但是,您也可以在自己的功能變數名稱(如**contoso.com)** 上公開應用程式。 本文說明如何保留或設定雲端服務 Web 角色的自訂網域名稱。

您已經了解什麼是 CNAME 和 A 記錄嗎？ [跳過說明](#add-a-cname-record-for-your-custom-domain)。

> [!NOTE]
> 此工作的程序適用於 Azure 雲端服務。 對於應用程式服務，請參閱[將現有的自訂 DNS 名稱對應至 Azure Web Apps](../app-service/app-service-web-tutorial-custom-domain.md)。 對於儲存體帳戶，請參閱[針對 Azure Blob 儲存體端點設定自訂網域名稱](../storage/blobs/storage-custom-domain-name.md)。
> 
> 

<p/>

> [!TIP]
> 快速完成啟用 -- 使用全新的 Azure [引導式逐步解說](https://support.microsoft.com/kb/2990804)！  它使將自定義功能變數名稱關聯並保護與 Azure 雲端服務或 Azure 網站的通訊(TLS) 成為一個快照。
> 
> 

## <a name="understand-cname-and-a-records"></a>了解 CNAME 和 A 記錄
CNAME (或別名記錄) 和 A 記錄都可讓您將網域名稱和特定的伺服器 (在此案例中為服務) 產生關聯，但運作方式不同。 針對 Azure 雲端服務來使用 A 記錄時，在決定使用何者之前，還有一些事項應該考慮。

### <a name="cname-or-alias-record"></a>CNAME 或別名記錄
CNAME 記錄將*特定*域(如**contoso.com**或**\.wwwcontoso.com)** 映射到規範的網域名稱。 在此案例中，Canonical 網域名稱為 Azure 主控應用程式的 **[myapp].cloudapp.net** 網域名稱。 CNAME 建立之後還會建立 **[myapp].cloudapp.net**的別名。 CNAME 項目會自動解析成 **[myapp].cloudapp.net** 服務的 IP 位址，就算雲端服務的 IP 位址變更，您也不需要採取任何動作。

> [!NOTE]
> 某些域註冊商僅允許您在使用 CNAME 記錄(\.如 www contoso.com)時映射子域,而不是根名稱(如contoso.com。 如需 CNAME 記錄的詳細資訊，請參閱註冊機構提供的文件、[維基百科 CNAME 記錄條目](https://en.wikipedia.org/wiki/CNAME_record)，或 [IETF 網域名稱 - 實作與規格](https://tools.ietf.org/html/rfc1035)文件。

### <a name="a-record"></a>A 記錄
*記錄*將網域(如**contoso.com**或**www\.contoso.com)***或通配符域*(如**\*.contoso.com)** 映射到 IP 位址。 以 Azure 雲端服務而言，就是指服務的虛擬 IP。 因此,A 記錄在 CNAME 記錄上的主要好處是,您可以有一個使用通配符的\*條目, 例如 **.contoso.com**,該條目將處理多個子域(如**mail.contoso.com、login.contoso.com**或**www\.contso.com)** 的請求。 **login.contoso.com**

> [!NOTE]
> 因為 A 記錄會對應至靜態 IP 位址，所以無法自動解析雲端服務 IP 位址的變更。 雲服務使用的 IP 位址是在您第一次部署到空插槽(生產或暫存)時分配的。如果刪除該槽的部署,Azure將釋放 IP 位址,並且將來對該槽的任何部署都可能獲得新的 IP 位址。
> 
> 在預備和生產部署之間切換時，或就地升級現有的部署時，指定部署位置 (生產或預備) 的 IP 位址會保留下來，相當方便。 如需關於執行這些動作的詳細資訊，請參閱 [如何管理雲端服務](cloud-services-how-to-manage-portal.md)。
> 
> 

## <a name="add-a-cname-record-for-your-custom-domain"></a>新增自訂網域的 CNAME 記錄
若要建立 CNAME 記錄，您必須使用註冊機構提供的工具，在 DNS 表格中為自訂網域新增項目。 各註冊機構指定 CNAME 記錄的方法都很類似，只是稍微不同，但概念都一樣。

1. 使用其中一種方法來尋找指派給雲端服務的 **.cloudapp.net** 網域名稱。

   * 登錄到[Azure 門戶],選擇雲服務,查看 **「概述」** 部分,然後查找**網站 URL**條目。

       ![快速瀏覽區段，其中顯示網站 URL][csurl]

       **OR**
   * 安裝並設定 [Azure Powershell](/powershell/azure/overview)，然後使用下列命令：

       ```powershell
       Get-AzureDeployment -ServiceName yourservicename | Select Url
       ```

     請將任一方法傳回的 URL 中所使用的網域名稱儲存下來，建立 CNAME 記錄時需要用到。
2. 登入 DNS 註冊機構的網站，並移至 DNS 管理頁面。 在網站中尋找標示為 **Domain Name**、**DNS** 或 **Name Server Management** 的連結或區域。
3. 現在找出可選取或輸入 CNAME 的地方。 您可能需要從下拉式清單中或移至進階設定頁面，才能選取記錄類型。 請尋找 **CNAME**、**Alias** 或 **Subdomains** 之類的字。
4. 如果要為 www customdomain.com 創建別名,還必須提供 CNAME 的域或子域別名,例如**www****\.www。** 如果要為根域創建別名,它可能在註冊商的 DNS 工具中**\@** 列為『 符號』符號。
5. 接著，您必須提供正式主機名稱，在此案例中為應用程式的 **cloudapp.net** 網域。

例如,以下 CNAME 記錄**\.** 將從 www contoso.com 轉寄到已部署應用程式的自訂網域名稱**contoso.cloudapp.net**的所有流量:

| 別名/主機名稱/子網域 | 正式網域 |
| --- | --- |
| www |contoso.cloudapp.net |

> [!NOTE]
> **www\.contoso.com**訪問者永遠不會看到真正的主機(contoso.cloudapp.net),因此轉發過程對最終使用者不可見。
> 
> 上述範例僅適用於 **www** 子網域的流量。 因為 CNAME 記錄不能使用萬用字元，所以您必須為每一個網域/子網域建立一個 CNAME。 如果要將來自子網域 (例如 *.contoso.com) 的流量導向您的 cloudapp.net 位址，您可以在 DNS 設定中設定 [URL 重新導向]**** 或 [URL 轉送]**** 項目，或建立 A 記錄。

## <a name="add-an-a-record-for-your-custom-domain"></a>新增自訂網域的 A 記錄
若要建立 A 記錄，您必須先找出雲端服務的虛擬 IP 位址。 然後，利用註冊機構提供的工具，在 DNS 表格中為自訂網域新增項目。 各註冊機構指定 A 記錄的方法都很類似，只是稍微不同，但概念都一樣。

1. 使用下列其中一種方法取得雲端服務的 IP 位址。

   * 登錄到[Azure 門戶],選擇雲服務,查看 **「概述」** 部分,然後查找**公共 IP 位址**條目。

       ![快速瀏覽區段，其中顯示 VIP][vip]

       **OR**
   * 安裝並設定 [Azure Powershell](/powershell/azure/overview)，然後使用下列命令：

       ```powershell
       get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip
       ```

     建立 A 記錄時需要用到此 IP 位址，請儲存下來。
2. 登入 DNS 註冊機構的網站，並移至 DNS 管理頁面。 在網站中尋找標示為 **Domain Name**、**DNS** 或 **Name Server Management** 的連結或區域。
3. 現在找出可選取或輸入 A 記錄的地方。 您可能需要從下拉式清單中或移至進階設定頁面，才能選取記錄類型。
4. 選取或輸入將使用此 A 記錄的網域或子網域。 例如,如果要為**\.www customdomain.com**創建別名,請選擇**www。** 如果要為所有子網域建立萬用字元項目，請輸入 '*****'。 這將涵蓋所有子域名,如**mail.customdomain.com、login.customdomain.com**和**login.customdomain.com****\.www customdomain.com。**

    如果要為根域創建 A 記錄,它可能在註冊商的 DNS 工具中**\@** 列為『符號。
5. 在提供的欄位中，輸入雲端服務的 IP 位址。 這樣會將 A 記錄中使用的網域項目與雲端服務部署的 IP 位址產生關聯。

例如，下列 A 記錄會將來自 **contoso.com** 的所有流量轉送至 **137.135.70.239** (已部署之應用程式的 IP 位址)：

| 主機名稱/子網域 | IP 位址 |
| --- | --- |
| \@ |137.135.70.239 |

此範例示範建立根網域的 A 記錄。 如果想要建立萬用字元項目來涵蓋所有子網域，請輸入 '*****' 做為子網域。

> [!WARNING]
> 依預設，Azure 中的 IP 位址是動態的。 您可能想要使用 [保留的 IP 位址](../virtual-network/virtual-networks-reserved-public-ip.md) ，以確保您的 IP 位址不會變更。
> 
> 

## <a name="next-steps"></a>後續步驟
* [如何管理雲服務](cloud-services-how-to-manage-portal.md)
* [如何將 CDN 內容對應至自訂網域](../cdn/cdn-map-content-to-custom-domain.md)
* [雲端服務的一般設定](cloud-services-how-to-configure-portal.md)。
* 了解如何 [部署雲端服務](cloud-services-how-to-create-deploy-portal.md)。
* 設定[TLS/SSL 憑證](cloud-services-configure-ssl-certificate-portal.md)。

[Expose Your Application on a Custom Domain]: #access-app
[Add a CNAME Record for Your Custom Domain]: #add-cname
[Expose Your Data on a Custom Domain]: #access-data
[VIP swaps]: cloud-services-how-to-manage-portal.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production
[Create a CNAME record that associates the subdomain with the storage account]: #create-cname
[Azure 入口網站]: https://portal.azure.com
[vip]: ./media/cloud-services-custom-domain-name-portal/csvip.png
[csurl]: ./media/cloud-services-custom-domain-name-portal/csurl.png




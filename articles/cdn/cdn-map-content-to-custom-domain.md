---
title: 教學課程：將自訂網域新增至您的端點
titleSuffix: Azure Content Delivery Network
description: 使用本教學課程，將自訂網域新增至 Azure 內容傳遞網路端點，以便在您的 URL 中顯示網域名稱。
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.topic: tutorial
ms.date: 11/06/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 03ed47ee97f52aca708118f202fad583753549bf
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "94331194"
---
# <a name="tutorial-add-a-custom-domain-to-your-endpoint"></a>教學課程：將自訂網域新增至您的端點

本教學課程說明如何將自訂網域新增至 Azure 內容傳遞網路 (CDN) 端點。 

CDN 設定檔中的端點名稱是 azureedge.net 的子網域。 根據預設，傳遞內容時，CDN 設定檔網域會包含在 URL 中。

例如： **https://contoso.azureedge.net/photo.png** 。

Azure CDN 會提供在自訂網域與 CDN 端點之間建立關聯的選項。 此選項會在 URL 中使用自訂網域來傳遞內容，而不是使用預設網域。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
> - 建立 CNAME DNS 記錄。
> - 將自訂網域與您的 CDN 端點產生關聯。
> - 驗證自訂網域。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

* 您必須建立 CDN 設定檔和至少一個 CDN 端點，才能完成本教學課程中的步驟。 
    * 如需詳細資訊，請參閱[快速入門：建立 Azure CDN 設定檔和端點](cdn-create-new-endpoint.md)。

* 如果您沒有自訂網域，請向網域提供者購買一個。 
    * 如需詳細資訊，請參閱[購買自訂網域名稱](../app-service/manage-custom-dns-buy-domain.md)。

* 如果您使用 Azure 來裝載 [DNS 網域](../dns/dns-overview.md)，請將您的自訂網域委派給 Azure DNS。 
    * 如需詳細資訊，請參閱[將網域委派給 Azure DNS](../dns/dns-delegate-domain-azure-dns.md)。

* 如果您使用網域提供者來處理 DNS 網域，請繼續[建立 CNAME DNS 記錄](#create-a-cname-dns-record)。


## <a name="create-a-cname-dns-record"></a>建立 CNAME DNS 記錄

您必須先建立正式名稱 (CNAME) 記錄，並將 Azure DNS 或 DNS 提供者指向您的 CDN 端點，才可在 Azure CDN 端點使用自訂網域。 

CNAME 記錄是一種將來源網域名稱對應至目的地網域名稱的 DNS 記錄。 

在 Azure CDN 中，來源網域名稱是您的自訂網域名稱，而目的地網域名稱是您的 CDN 端點主機名稱。 

在驗證 CNAME 記錄之後，Azure CDN 會將定址至來源自訂網域的流量路由至目的地 CDN 端點主機名稱。

自訂網域及其子網域一次只能與單一端點相關聯。 

對於不同的 Azure 服務，請將多個 CNAME 記錄用於來自相同自訂網域的不同子網域。

您可以將具有不同子網域的自訂網域對應至相同的 CDN 端點。

> [!NOTE]
> 本教學課程使用 CNAME 記錄類型。 如果您使用 A 或 AAAA 記錄類型，請遵循以下相同步驟並以您選擇的記錄類型取代 CNAME。

---
# <a name="azure-dns"></a>[**Azure DNS**](#tab/azure-dns)

對於相同訂用帳戶內的 Azure 資源，Azure DNS 會使用別名記錄。

若要為您的 Azure CDN 端點新增別名記錄：

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 在左側功能表中選取 [所有資源]，然後選取自訂網域的 Azure DNS 區域。

3. 在自訂網域的 DNS 區域中，選取 [+ 記錄集]。

4. 在 [新增記錄集] 中，輸入或選取下列資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 名稱  | 輸入您要用於 CDN 端點的別名。 例如 **www**。 |
    | 類型  | 選取 **CNAME**。 |
    | 別名記錄集 | 選取 [是]。 |
    | 別名類型 | 選取 [Azure 資源]。 |
    | 選擇訂用帳戶 | 選取您的訂用帳戶。 |
    | Azure 資源 | 選取您的 Azure CDN 端點。 |

5. 將記錄的 **TTL** 變更為您的值。

6. 選取 [確定]。

# <a name="dns-provider"></a>[**DNS 提供者**](#tab/dns-provider)

## <a name="map-the-temporary-cdnverify-subdomain"></a>對應暫時 cdnverify 子網域

在對應生產環境中的現有網域時，會有特殊事項需要考量。 

當您在 Azure 入口網站中註冊自訂網域時，網域可能會有短暫的期間停止運作。

為了避免 Web 流量中斷，請將自訂網域對應至具有 Azure **cdnverify** 子網域的 CDN 端點主機名稱。 此程序會建立暫時 CNAME 對應。 

透過此方法，使用者在 DNS 對應期間將可持續存取您的網域而不被中斷。 

如果沒有生產環境停機方面的顧慮，您可以直接將自訂網域對應至您的 CDN 端點。 請繼續[對應永久自訂網域](#map-the-permanent-custom-domain)。

建立 cdnverify 子網域的 CNAME 記錄：

1. 登入自訂網域的 DNS 提供者網站。

2. 建立自訂網域的 CNAME 記錄項目，並完成下表說明的欄位 (欄位名稱可能有所不同)：

    | 來源                    | 類型  | Destination                     |
    |---------------------------|-------|---------------------------------|
    | cdnverify. www.contoso.com | CNAME | cdnverify.contoso.azureedge.net |

    - 來源：以下列格式輸入您的自訂網域名稱 (包括 cdnverify 子網域)：**cdnverify.\<custom-domain-name>**
        - 例如：**cdnverify. www.contoso.com**

    - 輸入：輸入或選取 **CNAME**。

    - 目的地：以下列格式輸入您的 CDN 端點主機名稱 (包括 cdnverify 子網域)：**cdnverify.\<endpoint-name>.azureedge.net**。 
        - 例如：**cdnverify.contoso.azureedge.net**

3. 儲存您的變更。

## <a name="map-the-permanent-custom-domain"></a>對應永久自訂網域

在本節中，您會將永久自訂網域對應至 CDN 端點。 

若要建立自訂網域的 CNAME 記錄：

1. 登入自訂網域的網域提供者網站。

2. 建立自訂網域的 CNAME 記錄項目，並完成下表說明的欄位 (欄位名稱可能有所不同)：

    | 來源          | 類型  | Destination           |
    |-----------------|-------|-----------------------|
    | [www.contoso.com](www.contoso.com) | CNAME | contoso.azureedge.net |

    - 來源：輸入您的自訂網域名稱。
        - 例如：**www.contoso.com**

    - 輸入：輸入或選取 **CNAME**。

    - 目的地：以下列格式輸入您的 CDN 端點主機名稱： **\<endpoint-name>.azureedge.net**。 
        - 例如：**contoso.azureedge.net**

3. 儲存您的變更。

4. 如果您先前曾建立暫時 cdnverify 子網域 CNAME 記錄，請將其刪除。 

5. 如果您是第一次在生產環境中使用此自訂網域，請遵循[將自訂網域與您的 CDN 端點產生關聯](#associate-the-custom-domain-with-your-cdn-endpoint)和[驗證自訂網域](#verify-the-custom-domain)的步驟。

---
## <a name="associate-the-custom-domain-with-your-cdn-endpoint"></a>將自訂網域與您的 CDN 端點產生關聯

在註冊您的自訂網域之後，您可以將其新增至 CDN 端點。 

1. 登入 [Azure 入口網站](https://portal.azure.com/)，然後瀏覽至要對應到自訂網域的端點所在的 CDN 設定檔。
    
2. 在 [CDN 設定檔]  頁面上，選取要與自訂網域產生關聯的 CDN 端點。

    :::image type="content" source="media/cdn-map-content-to-custom-domain/cdn-endpoint-selection.png" alt-text="CDN 端點選取" border="true":::
    
3. 選取 [+ 自訂網域]。 

   :::image type="content" source="media/cdn-map-content-to-custom-domain/cdn-custom-domain-button.png" alt-text="新增自訂網域按鈕" border="true":::

4. 在 [新增自訂網域] 中，[端點主機名稱] 會預先填入，並衍生自您的 CDN 端點 URL： **\<endpoint-hostname>** .azureedge.net。 無法予以變更。

5. 針對 [自訂主機名稱]  ，請輸入您的自訂網域 (包括子網域)，以作為 CNAME 記錄的來源網域。 
    1. 例如 **www.contoso.com** 或 **cdn.contoso.com**。 **請勿使用 cdnverify 子網域名稱**。

    :::image type="content" source="media/cdn-map-content-to-custom-domain/cdn-add-custom-domain.png" alt-text="新增自訂網域" border="true":::

6. 選取 [新增]  。

   Azure 會確認您所輸入的自訂網域名稱有 CNAME 記錄存在。 如果 CNAME 正確，就會驗證您的自訂網域。 

   新的自訂網域設定傳播至所有 CDN 邊緣節點可能需要一些時間： 
    - 若為 **來自 Microsoft 的標準 Azure CDN** 設定檔，通常會在 10 分鐘內完成傳播。 
    - 若為 **來自 Akamai 的標準 Azure CDN** 設定檔，通常會在一分鐘內完成傳播。 
    - 若為 **來自 Verizon 的標準 Azure CDN** 和 **來自 Verizon 的進階 Azure CDN** 設定檔，通常會在 10 分鐘內完成傳播。   


## <a name="verify-the-custom-domain"></a>驗證自訂網域

完成註冊自訂網域之後，請確認自訂網域會參考您的 CDN 端點。
 
1. 確定您有在端點上快取的公用內容。 例如，如果您的 CDN 端點與儲存體帳戶相關聯，則 Azure CDN 會快取公用容器中的內容。 請將您的容器設定為允許公用存取，且其中至少包含一個檔案可用來測試自訂網域。

2. 在瀏覽器中，使用自訂網域瀏覽至檔案的位址。 例如，如果您的自訂網域是 `www.contoso.com`，則快取檔案的 URL 應與以下 URL 類似：`http://www.contoso.com/my-public-container/my-file.jpg`。 確認結果與您直接在 **\<endpoint-hostname>** .azureedge.net 上存取 CDN 端點時相同。

## <a name="clean-up-resources"></a>清除資源

如果您不想再讓您的端點與自訂網域相關聯，請執行下列步驟以移除自訂網域：
 
1. 在您的 CDN 設定檔中，選取您要移除的自訂網域所在的端點。

2. 在 [端點]  頁面的 [自訂網域] 下，以滑鼠右鍵按一下您要移除的自訂網域，然後從內容功能表中選取 [刪除]  。 選取 [是]。

   自訂網域會與您的端點解除關聯。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> - 建立 CNAME DNS 記錄。
> - 將自訂網域與您的 CDN 端點產生關聯。
> - 驗證自訂網域。

請移至下一個教學課程，以了解如何在 Azure CDN 自訂網域上設定 HTTPS。

> [!div class="nextstepaction"]
> [教學課程：在 Azure CDN 自訂網域上設定 HTTPS](cdn-custom-ssl.md)
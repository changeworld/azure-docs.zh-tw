---
title: 將根或頂點網域上架到現有的 Azure CDN 端點-Azure 入口網站
description: 瞭解如何使用 Azure 入口網站將根或頂點網域上線至現有的 Azure CDN 端點。
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 11/07/2020
ms.author: allensu
ms.openlocfilehash: 8ab4f698c7149d8d57f790e221ccbe35ec090fe6
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2020
ms.locfileid: "94370128"
---
# <a name="onboard-a-root-or-apex-domain-to-an-existing-azure-cdn-endpoint"></a>將根或頂點網域上架到現有的 Azure CDN 端點

Azure CDN 會使用 CNAME 記錄來驗證網域擁有權，以進行自訂網域的上架。 CDN 不會公開與 CDN 設定檔相關聯的前端 IP 位址。 如果您的目的是要將頂點網域上架到 Azure CDN，則無法將其對應至 IP 位址。

DNS 通訊協定可防止在區域頂點指派 CNAME 記錄。 例如，如果您的網域是 `contoso.com` ; 您可以建立的 cname 記錄， `somelabel.contoso.com` 但無法為 `contoso.com` 其本身建立 cname。 如果應用程式擁有者的 Azure CDN 背後有負載平衡應用程式，這種限制就會造成問題。 由於使用 CDN 設定檔需要建立 CNAME 記錄，因此無法從區域頂點指向 CDN 設定檔。

您可以使用 Azure DNS 中的別名記錄來解決此問題。 不同于 CNAME 記錄，別名記錄是在區域頂點中建立。 應用程式擁有者可以使用它，將其區域頂點記錄指向具有公用端點的 CDN 設定檔。 應用程式擁有者會指向其 DNS 區域內用於任何其他網域的相同 CDN 設定檔。 例如， `contoso.com` 和 `www.contoso.com` 可以指向相同的 CDN 設定檔。 

將您的頂點或根域對應至您的 CDN 設定檔，需要 CNAME 簡維或 DNS 追蹤。 一種機制，可讓 DNS 提供者以遞迴方式解析 CNAME 專案，直到達到 IP 位址為止。 CDN 端點的 Azure DNS 支援此功能。 

> [!NOTE]
> 另外還有其他 DNS 提供者也支援 CNAME 簡維或 DNS 追蹤，不過，Azure CDN 建議您使用 Azure DNS 給其客戶來裝載其網域。

您可以使用 Azure 入口網站將 CDN 上的頂點網域上線，並將其與用於 TLS 終止的憑證產生關聯，以在其上啟用 HTTPS。 頂點網域也稱為根或 naked 網域。

## <a name="create-an-alias-record-for-zone-apex"></a>建立區域頂點的別名記錄

1. 開啟要上線之網域的 **Azure DNS** 設定。

2. 選取 [+ 記錄集]。

3. 在 [ **新增記錄集** ] 中，輸入或選取下列資訊：

    | 設定 | 值 |
    | ------- | ------|
    | 名稱 | 輸入 **@** 。 |
    | 類型 | 選取 **。** |
    | 別名記錄集 | 選取 [是]。 |
    | 別名類型 | 選取 [ **Azure 資源** ]。 |
    | 選擇訂用帳戶 | 選取您的訂用帳戶。 |
    | Azure 資源 | 選取您的 CDN 端點。 |

4. 輸入 **TTL** 的值。

5. 選取 **[確定]** 以提交變更。

    :::image type="content" source="./media/onboard-apex-domain/cdn-apex-alias-record.png" alt-text="區域頂點的別名記錄":::

6. 上述步驟會建立指向 CDN 資源的區域頂點記錄。 CNAME 記錄對應 **cdnverify** 可用來將 CDN 設定檔上的網域上線。
    1. 範例， **cdnverify.contoso.com** 。
    

## <a name="onboard-the-custom-domain-on-your-cdn"></a>在 CDN 上讓自訂網域上線

在註冊您的自訂網域之後，您可以將其新增至 CDN 端點。 

1. 登入 [Azure 入口網站](https://portal.azure.com/)，然後瀏覽至要對應到自訂網域的端點所在的 CDN 設定檔。
    
2. 在 [CDN 設定檔]  頁面上，選取要與自訂網域產生關聯的 CDN 端點。

    :::image type="content" source="media/onboard-apex-domain/cdn-endpoint-selection.png" alt-text="CDN 端點選取專案" border="true":::
    
3. 選取 [ **+ 自訂網域** ]。 

   :::image type="content" source="media/onboard-apex-domain/cdn-custom-domain-button.png" alt-text="新增自訂網域按鈕" border="true":::

4. 在 [ **新增自訂網域** ] 中， **端點主機名稱** 會預先填入，並且衍生自您的 CDN 端點 URL： **\<endpoint-hostname>** . azureedge.net。 無法予以變更。

5. 針對 [ **自訂主機名稱** ]，輸入您的自訂根或頂點網域，作為 CNAME 記錄的來源網域。 
    1. 例如， **contoso.com** 。 **請勿使用 cdnverify 子功能變數名稱稱** 。

    :::image type="content" source="media/onboard-apex-domain/cdn-add-custom-domain.png" alt-text="新增自訂網域" border="true":::

6. 選取 [新增]  。

   Azure 會確認您所輸入的自訂網域名稱有 CNAME 記錄存在。 如果 CNAME 正確，就會驗證您的自訂網域。 

   新的自訂網域設定傳播至所有 CDN 邊緣節點可能需要一些時間： 
    - 若為 **來自 Microsoft 的標準 Azure CDN** 設定檔，通常會在 10 分鐘內完成傳播。 
    - 若為 **來自 Akamai 的標準 Azure CDN** 設定檔，通常會在一分鐘內完成傳播。 
    - 若為 **來自 Verizon 的標準 Azure CDN** 和 **來自 Verizon 的進階 Azure CDN** 設定檔，通常會在 10 分鐘內完成傳播。   

## <a name="enable-https-on-your-custom-domain"></a>在您的自訂網域上啟用 HTTPS

如需在 Azure CDN 的自訂網域上啟用 HTTPS 的詳細資訊，請參閱 [教學課程：在 AZURE cdn 自訂網域上設定 HTTPs](cdn-custom-ssl.md)

## <a name="next-steps"></a>後續步驟

- 瞭解如何 [建立 CDN 端點](cdn-create-new-endpoint.md)。

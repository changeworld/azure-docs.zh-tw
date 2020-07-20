---
title: 將根或頂點網域上架到現有的 Front 門-Azure 入口網站
description: 瞭解如何使用 Azure 入口網站將根或頂點網域上架至現有的大門。
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: how-to
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: d8f08f7cde54aaf705872c8c45bc18eb4a27df77
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84743587"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>在 Front Door 上將根或 apex 網域上線
Azure Front 門板會使用 CNAME 記錄來驗證網域擁有權，以進行自訂網域的上架。 此外，Front 門不會公開與您的前門設定檔相關聯的前端 IP 位址，因此如果您的頂點網域是要將它上線至 Azure Front，就無法將其對應到 IP 位址。

DNS 通訊協定可防止在區域頂點指派 CNAME 記錄。 例如，如果您的網域是， `contoso.com` 您可以建立的 cname 記錄， `somelabel.contoso.com` 但無法為 `contoso.com` 其本身建立 cname。 這種限制會針對在 Azure Front 後方具有負載平衡應用程式的應用程式擁有者帶來問題。 由於使用 Front 門板設定檔需要建立 CNAME 記錄，因此無法從區域頂點指向 Front 設定檔。

這個問題是使用 Azure DNS 上的別名記錄來解決。 不同于 CNAME 記錄，別名記錄會在區域頂點建立，而應用程式擁有者可以使用它將其區域頂點記錄指向具有公用端點的 Front 設定檔。 應用程式擁有者會指向其 DNS 區域內的任何其他網域所使用的同一個前門設定檔。 例如， `contoso.com` 和 `www.contoso.com` 可以指向相同的 Front 門設定檔。 

將您的頂點或根域對應至您的 Front 門設定檔，基本上需要 CNAME 簡維或 DNS 追蹤，這是 DNS 提供者中的機制，會以遞迴方式解析 CNAME 專案，直到它達到 IP 位址為止。 適用于 Front 門板的 Azure DNS 支援這項功能。 

> [!NOTE]
> 還有其他 DNS 提供者支援 CNAME 簡維或 DNS 追蹤，不過，Azure Front 門板建議為其客戶使用 Azure DNS 來裝載其網域。

您可以使用 Azure 入口網站將頂點網域上線，並將其與憑證建立關聯以進行 TLS 終止，以在其上啟用 HTTPS。 頂點網域也稱為 root 或 naked 網域。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 建立指向您的前門設定檔的別名記錄
> * 將根域新增至前門
> * 在根域上設定 HTTPS

> [!NOTE]
> 本教學課程會要求您已建立 Front 門板設定檔。 請參閱其他教學課程，例如[快速入門：建立 Front 門](./quickstart-create-front-door.md) [，或使用 HTTP 至 HTTPS](./front-door-how-to-redirect-https.md)重新導向來建立 front，以開始進行。

## <a name="create-an-alias-record-for-zone-apex"></a>建立區域頂點的別名記錄

1. 開啟要上架之網域的**Azure DNS**設定。
2. 建立或編輯區域頂點的記錄。
3. 選取記錄**類型**_做為_記錄，然後針對 [**別名記錄集** _] 選取 [是]_ 。 **別名類型**應設定為_Azure 資源_。
4. 選擇託管 Front 設定檔的 Azure 訂用帳戶，然後從 [ **azure 資源**] 下拉式清單中選取 [Front] 資源。
5. 按一下 **[確定]** 以提交變更。

    ![區域頂點的別名記錄](./media/front-door-apex-domain/front-door-apex-alias-record.png)

6. 上述步驟會建立指向您的 Front 門板資源的區域頂點記錄，以及 `afdverify.contosonews.com` `afdverify.<name>.azurefd.net` 將用來在您的前門設定檔上上線網域的 CNAME 記錄對應 ' afdverify ' （範例-）。

## <a name="onboard-the-custom-domain-on-your-front-door"></a>在您的 Front 中將自訂網域上線

1. 在 [Front 入口設計工具] 索引標籤上，按一下 [前端主機] 區段上的 [+] 圖示，以新增自訂網域。
2. 在 [自訂主機名稱] 欄位中輸入根或頂點功能變數名稱，例如 `contosonews.com` 。
3. 一旦將來自網域的 CNAME 對應驗證完成後，請按一下 [**新增**] 以新增自訂網域。
4. 按一下 [**儲存**] 以提交變更。

![[自訂網域] 功能表](./media/front-door-apex-domain/front-door-onboard-apex-domain.png)

## <a name="enable-https-on-your-custom-domain"></a>在您的自訂網域上啟用 HTTPS

1. 按一下已新增的自訂網域，然後在 [**自訂網域 HTTPS**] 區段下，將狀態變更為 [**已啟用**]。
2. 選取**憑證管理類型**為 [_使用我自己的憑證_]。

> [!WARNING]
> 頂點或根域目前不支援 Front 門板受控憑證管理類型。 可用來在頂點或根域上啟用 HTTPS 以供 Front 的唯一選項，就是使用您自己的自訂 TLS/SSL 憑證（裝載于 Azure Key Vault 上）。

3. 請確定您已設定適當的前端許可權以存取您的金鑰保存庫（如 UI 中所述），然後再繼續進行下一個步驟。
4. 從您目前的訂用帳戶中選擇**Key Vault 帳戶**，然後選取適當的**秘密**和**密碼版本**以對應至正確的憑證。
5. 按一下 [**更新**] 以儲存選取專案，然後按一下 [**儲存**]。
6. 在**幾分鐘後按一下**[重新整理]，然後再次按一下自訂網域，以查看憑證布建的進度。 

> [!WARNING]
> 請確定您已為頂點網域建立適當的路由規則，或已將網域新增至現有的路由規則。

## <a name="next-steps"></a>後續步驟

- 了解如何[建立 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的運作方式](front-door-routing-architecture.md)。
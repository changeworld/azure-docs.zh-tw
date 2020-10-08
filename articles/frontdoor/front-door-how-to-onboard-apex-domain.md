---
title: 將根或頂點網域上架到現有的 Front Door Azure 入口網站
description: 瞭解如何使用 Azure 入口網站將根或頂點網域上線至現有的 Front Door。
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: 44813a7662420ab4dedcd0bf99cc1eec7e9d9d2d
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91819086"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>在 Front Door 上將根或 apex 網域上線
Azure Front Door 使用 CNAME 記錄來驗證網域擁有權，以進行自訂網域的上架。 Front Door 不會公開與您 Front Door 設定檔相關聯的前端 IP 位址。 因此，如果您的目的是要將頂點網域上架到 Azure Front Door，您就無法將其對應至 IP 位址。

DNS 通訊協定可防止在區域頂點指派 CNAME 記錄。 例如，如果您的網域是 `contoso.com` ; 您可以建立的 cname 記錄， `somelabel.contoso.com` 但無法為 `contoso.com` 其本身建立 cname。 這種限制對於在 Azure Front Door 後方有負載平衡應用程式的應用程式擁有者有問題。 由於使用 Front Door 設定檔需要建立 CNAME 記錄，因此無法從區域頂點指向 Front Door 設定檔。

您可以使用 Azure DNS 中的別名記錄來解決此問題。 不同于 CNAME 記錄，別名記錄是在區域頂點中建立。 應用程式擁有者可以使用它，將其區域頂點記錄指向具有公用端點的 Front Door 設定檔。 應用程式擁有者會指向其 DNS 區域內用於任何其他網域的相同 Front Door 設定檔。 例如， `contoso.com` 和 `www.contoso.com` 可以指向相同的 Front Door 設定檔。 

將您的頂點或根域對應至 Front Door 設定檔，基本上需要進行 CNAME 簡維或 DNS 追蹤。 一種機制，可讓 DNS 提供者以遞迴方式解析 CNAME 專案，直到達到 IP 位址為止。 Front Door 端點的 Azure DNS 支援這項功能。 

> [!NOTE]
> 但也有其他 DNS 提供者支援 CNAME 簡維或 DNS 追蹤，但 Azure Front Door 建議使用 Azure DNS，讓其客戶裝載其網域。

您可以使用 Azure 入口網站將 Front Door 上的頂點網域上線，並將其與 TLS 終止的憑證建立關聯，以在其上啟用 HTTPS。 頂點網域也稱為根或 naked 網域。

## <a name="create-an-alias-record-for-zone-apex"></a>建立區域頂點的別名記錄

1. 開啟要上線之網域的 **Azure DNS** 設定。

1. 建立或編輯區域頂點的記錄。

1. 選取記錄**類型***作為記錄*，然後針對 [**別名記錄集** *] 選取 [是]* 。 **別名類型** 應設定為 *Azure 資源*。

1. 選取用來裝載 Front Door 設定檔的 Azure 訂用帳戶。 然後從 [ **Azure 資源** ] 下拉式清單中選取 Front Door 資源。

1. 選取 **[確定]** 以提交變更。

    :::image type="content" source="./media/front-door-apex-domain/front-door-apex-alias-record.png" alt-text="區域頂點的別名記錄":::

1. 上述步驟會建立指向 Front Door 資源的區域頂點記錄，也會建立一個 CNAME 記錄對應 ' afdverify ' (範例- `afdverify.contosonews.com`) ，以在您的 Front Door 設定檔上將網域上架。

## <a name="onboard-the-custom-domain-on-your-front-door"></a>在您的 Front Door 上架自訂網域

1. 在 [Front Door 設計工具] 索引標籤上，選取 [前端主機] 區段上的 [+] 圖示，以新增自訂網域。

1. 在 [自訂主機名稱] 欄位中輸入 root 或頂點功能變數名稱，範例 `contosonews.com` 。

1. 一旦驗證從網域到 Front Door 的 CNAME 對應之後，請選取 [ **新增** ] 以新增自訂網域。

1. 選取 [ **儲存** ] 以提交變更。

   :::image type="content" source="./media/front-door-apex-domain/front-door-onboard-apex-domain.png" alt-text="區域頂點的別名記錄":::

## <a name="enable-https-on-your-custom-domain"></a>在您的自訂網域上啟用 HTTPS

1. 選取已新增的自訂網域，並在 [ **自訂網域 HTTPS**] 區段下，將 [狀態] 變更為 [ **已啟用**]。

1. 選取 [*使用我自己的憑證*] 的**憑證管理類型**。

   :::image type="content" source="./media/front-door-apex-domain/front-door-onboard-apex-custom-domain.png" alt-text="區域頂點的別名記錄":::    

   > [!WARNING]
   > 頂點或根域目前不支援 Front Door 受控憑證管理類型。 針對 Front Door 在頂點或 root 網域上啟用 HTTPS 的唯一可用選項，是使用您自己在 Azure Key Vault 上裝載的自訂 TLS/SSL 憑證。

1. 繼續進行下一個步驟之前，請確定您已設定正確的許可權，讓 Front Door 存取您的金鑰保存庫（如 UI 中所述）。

1. 選擇您目前訂用帳戶中的 **Key Vault 帳戶** ，然後選取適當的 **秘密** 和 **秘密版本** 以對應至正確的憑證。

1. 選取 [ **更新** ] 以儲存選取專案，然後選取 [ **儲存**]。

1. 選取 [重新整理幾分鐘後再重新整理]，然後 **再次選取自** 定義網域以查看憑證布建的進度。 

> [!WARNING]
> 確定您已為頂點網域建立適當的路由規則，或已將網域新增至現有的路由規則。

## <a name="next-steps"></a>後續步驟

- 了解如何[建立 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的運作方式](front-door-routing-architecture.md)。

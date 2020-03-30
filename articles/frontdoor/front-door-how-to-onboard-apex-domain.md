---
title: 將根域或頂點域添加到現有前門 - Azure 門戶
description: 瞭解如何使用 Azure 門戶將根域或頂點域板載到現有前門。
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: bb1042e15d4366923174996388eeb2fb99aef429
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74184621"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>在前門上板載根或頂點域
Azure 前門使用 CNAME 記錄來驗證域擁有權以用於加入自訂域。 此外，前門不會公開與前門設定檔關聯的前端 IP 位址，因此，如果意圖是將其板載到 Azure 前門，則無法將頂點域映射到 IP 位址。

DNS 通訊協定可防止在區域頂點指派 CNAME 記錄。 例如，如果您的域為`contoso.com`。可以為 創建`somelabel.contoso.com`CNAME 記錄。但您無法為自己`contoso.com`創建 CNAME。 對於在 Azure 前門後面具有負載平衡應用程式的應用程式擁有者來說，此限制帶來了問題。 由於使用前門設定檔需要創建 CNAME 記錄，因此無法從區域頂點指向前門設定檔。

此問題已使用 Azure DNS 上的別名記錄得到解決。 與 CNAME 記錄不同，別名記錄是在區域頂點創建的，應用程式擁有者可以使用它將區域頂點記錄指向具有公共終結點的前門設定檔。 應用程式擁有者指向用於其 DNS 區域內任何其他域的同一前門設定檔。 例如，`contoso.com`可以`www.contoso.com`指向相同的前門設定檔。 

將頂點或根域映射到前門設定檔基本上需要 CNAME 拼平或 DNS 追逐，這是 DNS 提供程式中遞迴地解析 CNAME 條目直到其到達 IP 位址的機制。 Azure DNS 支援用於前門端點的此功能。 

> [!NOTE]
> 還有其他 DNS 提供程式支援 CNAME 拼平或 DNS 追逐，但是，Azure 前門建議為其客戶使用 Azure DNS 為其託管其域。

您可以使用 Azure 門戶將前門上的頂點域上載，並通過將其與用於 SSL 終止的證書相關聯，在其中啟用 HTTPS。 頂點域也稱為根域或裸域。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 創建指向前門設定檔的別名記錄
> * 將根域添加到前門
> * 在根域上設置 HTTPS

> [!NOTE]
> 本教程要求您已創建前門設定檔。 參考其他教程，如[快速入門：創建前門](./quickstart-create-front-door.md)或[創建一個前門與HTTP到HTTPS重定向](./front-door-how-to-redirect-https.md)開始。

## <a name="create-an-alias-record-for-zone-apex"></a>為區域頂點創建別名記錄

1. 打開**要**上載的域的 Azure DNS 配置。
2. 創建或編輯區域頂點的記錄。
3. 選擇記錄**類型**作為_記錄_，然後為**別名記錄集**選擇 _"是_"。 **別名類型**應設置為_Azure 資源_。
4. 選擇託管前門設定檔的 Azure 訂閱，然後從**Azure 資源**下拉清單中選擇前門資源。
5. 按一下"**確定"** 以提交更改。

    ![區域頂點的別名記錄](./media/front-door-apex-domain/front-door-apex-alias-record.png)

6. 上述步驟將創建指向前門資源的區域頂點記錄，以及 CNAME 記錄映射"afdverify"（示例 -），`afdverify.contosonews.com``afdverify.<name>.azurefd.net`用於在前門設定檔上加入域。

## <a name="onboard-the-custom-domain-on-your-front-door"></a>在前門上的自訂域上

1. 在"前門設計器"選項卡上，按一下"前端主機"部分上的"+"圖示以添加新的自訂域。
2. 在自訂主機名稱欄位中輸入根或頂點功能變數名稱，示例`contosonews.com`。
3. 驗證從域到前門的 CNAME 映射後，按一下"**添加"** 以添加自訂域。
4. 按一下 **"保存"** 以提交更改。

![[自訂網域] 功能表](./media/front-door-apex-domain/front-door-onboard-apex-domain.png)

## <a name="enable-https-on-your-custom-domain"></a>在自訂域上啟用 HTTPS

1. 按一下已添加的自訂域，並在 **"自訂域 HTTPS"** 部分下將狀態更改為 **"已啟用**"。
2. 選擇**證書管理類型**以 _"使用我自己的證書"。_

> [!WARNING]
> 頂點或根域當前不支援前門託管證書管理類型。 在前門的頂點或根域上啟用 HTTPS 的唯一選項是使用託管在 Azure 金鑰保存庫上的自訂 SSL 憑證。

3. 確保在繼續下一步之前，已設置前門訪問金鑰保存庫的正確許可權，以便訪問 UI 中所述的金鑰保存庫。
4. 從當前訂閱中選擇**金鑰保存庫帳戶**，然後選擇適當的**機密**和**機密版本**以映射到正確的證書。
5. 按一下 **"更新"** 以保存所選內容，然後按一下"**保存**"。
6. 按一下幾分鐘後**刷新**，然後再次按一下自訂域以查看證書預配的進度。 

> [!WARNING]
> 確保已為頂點域創建了適當的路由規則，或將域添加到現有路由規則。

## <a name="next-steps"></a>後續步驟

- 了解如何[建立 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的運作方式](front-door-routing-architecture.md)。
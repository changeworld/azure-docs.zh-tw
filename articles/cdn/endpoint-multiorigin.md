---
title: Azure CDN 端點多來源
description: 開始使用多個來源的 Azure CDN 端點。
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.topic: how-to
ms.date: 8/20/2020
ms.author: allensu
ms.openlocfilehash: ed6c60b4f66361e87f67f3c64bb60846b2df757b
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88817367"
---
# <a name="azure-cdn-endpoint-multi-origin"></a>Azure CDN 端點多來源

多來源支援可減少停機時間，並建立全域冗余。 

藉由在 Azure CDN 端點內選擇多個來源，所提供的冗余會藉由探查每個來源的健康情況，並在必要時進行容錯移轉，來散佈風險。

設定一或多個原始群組，並選擇預設來源群組。 每個來源群組都是一個或多個來源的集合，可採用類似的工作負載。

> [!NOTE]
> 這項功能目前僅適用于來自 Microsoft 的 Azure CDN。 

## <a name="create-the-origin-group"></a>建立來源群組

1. 登入 [Azure 入口網站](https://portal.azure.com)

2. 選取您的 Azure CDN 設定檔，然後選取要設定多個來源的端點。

3. 在 [端點**設定**] 的 [設定] 底下選取 [**來源**]：

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-1.png" alt-text="CDN 端點" border="true":::

4. 若要啟用多個來源，您需要至少一個來源群組。 選取 [ **建立原始群組**]：

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-2.png" alt-text="原始設定" border="true":::

5. 在 [ **新增原始群組** ] 設定中，輸入或選取下列資訊：

   | 設定           | 值                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | 來源組名 | 輸入來源群組的名稱。                                   |
   | 探查狀態      | 選取 [啟用] 。 </br> Azure CDN 會從全球不同的點執行健康情況探查，以判斷原始健康情況。 如果目前的來源群組不在作用中，則不要啟用以避免產生額外的成本。
   | 探查路徑        | 來源中用來判斷健全狀況的路徑。 |
   | 探查間隔    | 選取1、2或4分鐘的探查間隔。                        |
   | 探查通訊協定    | 選取 [ **HTTP** ] 或 [ **HTTPS**]。                                         |
   | 探查方法      | 選取 [ **Head** ] 或 [ **Get**]。                                           |
   | 預設來源群組 | 選取要設為預設來源群組的方塊。
    
   :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-3.png" alt-text="新增來源群組" border="true":::

6. 選取 [新增]。

## <a name="add-multiple-origins"></a>新增多個來源

1. 在您端點的原始設定中，選取 [ **+ 建立來源**：

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-5.png" alt-text="建立來源" border="true":::

2. 在 [ **新增原始** 設定] 中，輸入或選取下列資訊：

   | 設定           | 值                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | 來源類型 | 選取 **儲存體**、 **雲端服務**、 **Web 應用程式**或 **自訂來源**。                                   |
   | 原始主機名稱        | 選取或輸入您的來源主機名稱。  下拉式清單會列出您在先前設定中指定之類型的所有可用原始來源。 如果您選取 [ **自訂原始來源** ] 作為來源類型，請輸入客戶源伺服器的網域。 |
   | 原始主機標頭    | 輸入您想要讓 Azure CDN 隨每個要求傳送的主機標頭，或保留預設值。                        |
   | HTTP 連接埠   | 輸入 HTTP 埠。                                         |
   | HTTPS 連接埠     | 輸入 HTTPS 埠。                                           |
   | 優先順序    | 請輸入介於1到5之間的數位。       |
   | Weight      | 請輸入介於1到1000之間的數位。   |

    > [!NOTE]
    > 在原始群組內建立來源時，必須根據優先順序和權數。 如果來源群組只有一個來源，則預設的 [優先順序] 和 [權數] 會設定為1。 如果來源狀況良好，流量會路由傳送至最高優先順序的來源。 如果來源判斷為狀況不良，則會以優先權順序將連接轉向另一個來源。 如果兩個來源具有相同的優先順序，則會根據針對來源指定的權數來散發流量 

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-6.png" alt-text="新增額外的來源" border="true":::

3. 選取 [新增]。

4. 選取 [設定 **來源** ] 以設定所有來源的原始路徑：

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-7.png" alt-text="設定來源路徑" border="true":::

5. 選取 [確定]。

## <a name="configure-origins-and-origin-group-settings"></a>設定來源和來源群組設定

當您有數個來源和一個來源群組之後，就可以在不同的群組中新增或移除該來源。 相同群組內的來源應該提供類似的工作負載。 流量會根據其狀況良好的狀態、優先順序和加權值散發至這些來源。 

1. 在 Azure CDN 端點的原始設定中，選取您要設定的來源組名：

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="設定來源和來源群組設定" border="true":::

2. 在 [ **更新來源] 群組**中，選取 [ **+ 選取來源**：

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-9.png" alt-text="更新原始群組" border="true":::

4. 在下拉式方塊中選取要新增至群組的來源，然後選取 **[確定]**。

5. 確認已將來源新增至群組，然後選取 [ **儲存**]：

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-10.png" alt-text="確認已將其他來源新增至群組" border="true":::

## <a name="remove-origin-from-origin-group"></a>從原始群組移除來源

1. 在 Azure CDN 端點的 [來源] 設定中，選取來源組名：

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="從群組移除來源" border="true":::

2. 若要從原始群組移除來源，請選取來源旁的垃圾桶圖示，然後選取 [ **儲存**]：

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-11.png" alt-text="更新原始群組刪除來源" border="true":::

## <a name="next-steps"></a>後續步驟
在本文中，您已啟用 Azure CDN 端點多來源。

如需有關 Azure CDN 和本文所述其他 Azure 服務的詳細資訊，請參閱：

* [Azure CDN](./cdn-overview.md)
* [比較 Azure CDN 產品功能](./cdn-features.md)

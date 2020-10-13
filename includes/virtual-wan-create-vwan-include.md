---
title: 包含檔案
description: 包含檔案
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/09/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5c9c8fa8099848eec09e689bf9491f71f378e9e3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90606939"
---
透過瀏覽器瀏覽至 Azure 入口網站，並使用您的 Azure 帳戶登入。

1. 瀏覽至 [虛擬 WAN] 頁面。 在入口網站中，按一下 [+建立資源]。 在搜尋方塊中鍵入**虛擬 WAN** 並選取 [輸入]。
1. 從結果中選取 [虛擬 WAN]。 在 [虛擬 WAN] 頁面中，按一下 [建立]，以開啟 [建立 WAN] 頁面。
1. 在 [建立 WAN] 頁面的 [基本] 索引標籤中，填寫下列欄位：

   :::image type="content" source="./media/virtual-wan-create-vwan-include/basics.png" alt-text="螢幕擷取畫面顯示 [建立 WAN] 窗格，並選取 [基本] 索引標籤。":::

   * **訂用帳戶** - 選取您要使用的訂用帳戶。
   * **資源群組** - 建立新的或使用現有的資源群組。
   * **資源群組位置** - 從下拉式清單中選擇資源位置。 WAN 是全域資源，並不會在特定區域存留。 不過，您必須選取一個區域以方便管理，以及放置所建立的 WAN 資源。
   * **名稱** - 鍵入要用來稱呼 WAN 的名稱。
   * **類型** - 基本或標準。 如果您建立基本 WAN，則只能建立基本中樞。 基本中樞只能進行 VPN 站對站連線。
1. 填寫完欄位之後，選取 [檢閱 + 建立]。
1. 驗證通過後，選取 [建立]，以建立虛擬 WAN。

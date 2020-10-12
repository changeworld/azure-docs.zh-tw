---
title: 在 Azure 防火牆中建立 IP 群組
description: IP 群組可讓您為 Azure 防火牆規則分組及管理 IP 位址。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/23/2020
ms.author: victorh
ms.openlocfilehash: c3ae62bf5b4f0b4796cac2e7079c8a09116d4895
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85602528"
---
# <a name="create-ip-groups"></a>建立 IP 群組

IP 群組可讓您為 Azure 防火牆規則分組及管理 IP 位址。 它們可以有單一 IP 位址、多個 IP 位址，或一或多個 IP 位址範圍。

## <a name="create-an-ip-group"></a>建立 IP 群組

1. 從 Azure 入口網站首頁，選取 [建立資源]。
2. 在 [搜尋] 文字方塊中輸入 **Ip 群組** ，然後選取 [ **ip 群組**]。
3. 選取 [建立]****。
4. 選取您的訂用帳戶。
5. 選取資源群組或建立新的資源群組。
6. 為您的 IP 群組輸入唯一的名稱，然後選取區域。

6. 完成時，選取 下一步:**IP 位址**。
7. 輸入 IP 位址、多個 IP 位址或 IP 位址範圍。

   有兩種方式可以輸入 IP 位址：
   - 您可以手動輸入
   - 您可以從檔案匯入它們

   若要從檔案匯入，請選取 [從檔案匯 **入**]。 您可以將檔案拖曳到方塊中，或選取 **[流覽檔案]**。 如有必要，您可以檢查和編輯已上傳的 IP 位址。

   當您輸入 IP 位址時，入口網站會進行驗證以檢查重迭、重複和格式化問題。

5. 完成時，選取 [ **審核 + 建立**]。
6. 選取 [建立]  。


## <a name="next-steps"></a>後續步驟

- [深入瞭解 IP 群組](ip-groups.md)
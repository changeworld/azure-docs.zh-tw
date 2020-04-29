---
title: 在 Azure 防火牆中建立 IP 群組
description: IP 群組可讓您分組和管理 Azure 防火牆規則的 IP 位址。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 7e8b2350b9e85d07ce1c399ce4536703ec998cbc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77444533"
---
# <a name="create-ip-groups-preview"></a>建立 IP 群組（預覽）

> [!IMPORTANT]
> 此公開預覽版是在沒有服務等級協定的情況下提供，不得用於生產工作負載。 可能不支援特定功能、可能已經限制功能，或者可能無法在所有 Azure 位置提供使用。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

IP 群組可讓您分組和管理 Azure 防火牆規則的 IP 位址。 它們可以有單一 IP 位址、多個 IP 位址，或一或多個 IP 位址範圍。

## <a name="create-an-ip-group"></a>建立 IP 群組

1. 從 Azure 入口網站首頁，選取 [建立資源]****。
2. 在 [搜尋] 文字方塊中輸入**Ip 群組**，然後選取 [ **ip 群組**]。
3. 選取 [建立]  。
4. 選取您的訂用帳戶。
5. 選取資源群組或建立新的資源群組。
6. 輸入 IP 群組的唯一名稱，然後選取區域。

6. 選取 **[下一步： IP 位址]**。
7. 輸入 IP 位址、多個 IP 位址或 IP 位址範圍。

   有兩種方式可輸入 IP 位址：
   - 您可以手動輸入它們
   - 您可以從檔案匯入它們

   若要從檔案匯入，請選取 [從檔案匯**入**]。 您可以將檔案拖曳到方塊中，或選取 **[流覽檔案]**。 如有需要，您可以審查和編輯您上傳的 IP 位址。

   當您輸入 IP 位址時，入口網站會驗證它，以檢查是否有重迭、重複和格式化的問題。

5. 完成後，選取 [審核] [ **+ 建立**]。
6. 選取 [建立]  。


## <a name="next-steps"></a>後續步驟

- [深入瞭解 IP 群組](ip-groups.md)
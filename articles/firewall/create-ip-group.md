---
title: 在 Azure 防火牆中創建 IP 組
description: IP 組允許您對 Azure 防火牆規則的 IP 位址進行分組和管理。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 7e8b2350b9e85d07ce1c399ce4536703ec998cbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444533"
---
# <a name="create-ip-groups-preview"></a>創建 IP 組（預覽版）

> [!IMPORTANT]
> 此公開預覽版是在沒有服務等級協定的情況下提供，不得用於生產工作負載。 可能不支援特定功能、可能已經限制功能，或者可能無法在所有 Azure 位置提供使用。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

IP 組允許您對 Azure 防火牆規則的 IP 位址進行分組和管理。 它們可以具有單個 IP 位址、多個 IP 位址或一個或多個 IP 位址範圍。

## <a name="create-an-ip-group"></a>創建 IP 組

1. 從 Azure 入口網站首頁，選取 [建立資源]****。
2. 在搜索文字方塊中鍵入**IP 組**，然後選擇**IP 組**。
3. 選取 [建立]****。
4. 選取您的訂用帳戶。
5. 選取資源群組或建立新的資源群組。
6. 鍵入 IP 組的唯一名稱，然後選擇區域。

6. 選擇**下一個：IP 位址**。
7. 鍵入 IP 位址、多個 IP 位址或 IP 位址範圍。

   有兩種方法可以輸入 IP 位址：
   - 您可以手動輸入它們
   - 可以從檔導入它們

   要從檔導入，請選擇**從檔導入**。 您可以將檔拖動到框中，也可以選擇 **"流覽檔**"。 如有必要，您可以查看和編輯上傳的 IP 位址。

   鍵入 IP 位址時，門戶會對其進行驗證，以檢查其重疊、重複和格式設置問題。

5. 完成後，選擇 **"審閱 + 創建**"。
6. 選取 [建立]****。


## <a name="next-steps"></a>後續步驟

- [瞭解有關 IP 組的更多](ip-groups.md)
---
title: Azure 區塊鏈權杖可組合性
description: Azure 區塊鏈權杖可組合性為高級方案創建權杖提供了靈活性。
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: a3fe1b290917de20b7c3af31fe386ed93580d850
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325109"
---
# <a name="azure-blockchain-tokens-composability"></a>Azure 區塊鏈權杖可組合性

[!INCLUDE [Preview note](./includes/preview.md)]

權杖可組合性為高級方案創建權杖提供了靈活性。 您可能有一個複雜的方案，無法使用[四個預構建的權杖範本](templates.md#base-token-types)實現。 權杖可組合性允許您通過添加或刪除定義的行為來構建自己的權杖範本來設計自己的權杖範本。 創建新權杖範本時，Azure 區塊鏈權杖將驗證所有權杖語法規則。 組合範本保存在 Azure 區塊鏈權杖服務中，用於在連接的區塊鏈網路上頒發。

您可以在以下部分中使用[權杖行為](templates.md#token-behaviors)來設計權杖範本。

## <a name="burnable-b"></a>可燒錄 （b）

能夠從電源中刪除權杖。

例如，當您為禮品卡兌換線上信用卡積分時，信用卡積分會被燒毀。

## <a name="delegable-g"></a>可委派 （g）

能夠委派對您擁有的權杖執行的操作。

委託可以作為權杖的擁有者執行操作。 例如，您可以使用可委派的權杖來實現投票。 可委派的權杖允許投票權杖擁有者讓其他人代表他們投票。

## <a name="logable-l"></a>可日誌 （l）

能夠登錄。

例如，您可以為每個放映特定影片的影院頒發電影分發的可日誌權杖。 要播放影片，放映必須記錄每個放映的事務，因為在影片上映期間，每次放映的版稅支出。 參與者生成可以使用影片權杖來驗證發行版本中每個影院放映的放映每個影片的支出。

## <a name="mint-able-m"></a>薄荷 （m）

能夠為權杖類鑄造其他權杖。 混用角色包括可小人的行為。

例如，想要實施忠誠度計畫的零售公司可以使用可分明的權杖來實施其忠誠度計畫。 隨著客戶群的增長，他們可以為客戶創造額外的忠誠度點。  

## <a name="non-subdividable-or-whole-d"></a>不可分或整體 （*d）

防止權杖被劃分為更小的部分的限制。

例如，單個藝術繪畫不能細分為多個較小的部分。 

## <a name="non-transferable-t"></a>不可轉讓 （+t）

防止從初始權杖擁有者更改擁有權的限制。

例如，大學文憑是不可轉讓的權杖。 一旦向畢業生頒發文憑，就不能從畢業生轉到另一個人。

## <a name="roles-r"></a>角色 （r）

能夠為特定行為在權杖範本類中定義角色。

可以提供權杖在權杖創建時支援的角色名稱清單。 指定角色後，使用者可以為這些行為分配角色。 目前，僅支援 minter 角色。

## <a name="singleton-s"></a>單頓 （s）

允許一個權杖的供應的限制。

例如，博物館文物是單一標記。 博物館文物是獨一無二的。 表示工件的權杖在供應中只有一個項。

## <a name="subdividable-d"></a>可分div （d）

能夠將權杖劃分為更小的部分。

例如，一美元可以細分為美分。

## <a name="transferable-t"></a>可轉讓 （t）

能夠轉移權杖的擁有權。

例如，屬性擁有權是可轉讓的權杖，在出售房產時，可以從一個人轉移到另一個人。

## <a name="next-steps"></a>後續步驟

瞭解[Azure 區塊鏈權杖帳戶管理](account-management.md)。

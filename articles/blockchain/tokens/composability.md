---
title: Azure 區塊鏈權杖複合性
description: Azure 區塊鏈 token 複合性提供彈性來建立高階案例的權杖。
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: a3fe1b290917de20b7c3af31fe386ed93580d850
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74325109"
---
# <a name="azure-blockchain-tokens-composability"></a>Azure 區塊鏈權杖複合性

[!INCLUDE [Preview note](./includes/preview.md)]

Token 複合性可讓您彈性地建立適用于 advanced 案例的權杖。 您可能會有一個不能使用[四個預先建立的權杖範本](templates.md#base-token-types)來執行的複雜案例。 Token 複合性可讓您藉由新增或移除定義的行為來設計自己的權杖範本，以建立您自己的權杖範本。 建立新的權杖範本時，Azure 區塊鏈 token 會驗證所有權杖文法規則。 組成的範本會儲存在 Azure 區塊鏈 token 服務中，以發出連線的區塊鏈網路。

您可以使用下列各節中的[權杖行為](templates.md#token-behaviors)來設計您的權杖範本。

## <a name="burnable-b"></a>Burnable （b）

能夠移除提供的權杖。

例如，當您兌換禮物卡的線上信用卡點時，會燒錄信用卡點。

## <a name="delegable-g"></a>可委派（g）

能夠委派您所擁有之權杖上所採取的動作。

委派可以執行動作做為權杖的擁有者。 例如，您可以使用可委派 token 來執行投票。 可委派 token 可讓投票權杖擁有者代表他人投票。

## <a name="logable-l"></a>Logable （l）

能夠進行記錄。

例如，您可以將電影發佈的 logable token 發行到每個顯示特定電影的劇院。 若要播放電影，顯示必須記錄每個顯示的交易，因為版稅支出是在電影的發行執行期間顯示。 動作專案組建可以使用電影權杖來驗證每個電影在散發中的支出。

## <a name="mint-able-m"></a>Mint 能夠（m）

能夠 mint 權杖類別的其他權杖。 Minter 角色包含 mintable 行為。

例如，想要實行忠誠度計畫的零售公司，可以針對其忠誠度計畫使用 mintable 權杖。 他們可以在客戶群成長時，為客戶 mint 額外的忠誠度積分。  

## <a name="non-subdividable-or-whole-d"></a>非 subdividable 或整體（~ d）

限制以防止權杖分割成較小的部分。

例如，單一美工圖案無法細分為多個較小的部分。 

## <a name="non-transferable-t"></a>不可轉移（~ t）

限制，以防止初始權杖擁有者變更擁有權。

例如，大學文憑是不可轉移的權杖。 一旦將文憑交給畢業，就無法從畢業轉移到其他人。

## <a name="roles-r"></a>角色（r）

能夠在權杖範本類別中定義特定行為的角色。

您可以在權杖建立時提供權杖支援的角色名稱清單。 當指定角色時，使用者可以將角色指派給這些行為。 目前僅支援 minter 角色。

## <a name="singleton-s"></a>單一（s）

允許提供一個權杖的限制。

例如，「博物館成品」是單一 token。 博物館構件是唯一的。 代表成品的 token 只有供應中的單一專案。

## <a name="subdividable-d"></a>Subdividable （d）

能夠將權杖分割成較小的部分。

例如，某個貨幣可以細分成美分。

## <a name="transferable-t"></a>可轉換（t）

能夠轉移權杖的擁有權。

例如，屬性標題是可轉移的權杖，當銷售該屬性時，可以從某個人員傳輸到另一個人。

## <a name="next-steps"></a>後續步驟

瞭解[Azure 區塊鏈 token 帳戶管理](account-management.md)。

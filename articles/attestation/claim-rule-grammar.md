---
title: Azure 證明宣告規則文法
description: Azure 證明原則宣告和宣告規則的詳細資料。
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 3ed5c3f8232047787c6f05628f1eef35a7533999
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91285413"
---
# <a name="claim-and-claim-rules"></a>宣告和宣告規則

若要了解宣告規則的文法，您必須先了解證明原則宣告。

## <a name="claim"></a>宣告

宣告是為提供相關資訊而群組在一起的一組屬性。 針對 Azure 證明，宣告包含下列屬性：

- **類型**：表示宣告類型的字串值。
- **value**：表示宣告值的布林值、整數值或字串值。
- **valueType**：儲存在 value 屬性中的資訊資料類型。 支援的類型為字串、整數和布林值。 如果未定義，預設值會是「字串」。
- **issuer**：宣告簽發者的相關資訊。 簽發者將會是下列其中一種類型：
  - **AttestationService**：Azure 證明可將某些宣告提供給原則作者使用，證明原則的作者可以利用其來製作適當的原則。
  - **AttestationPolicy**：原則 (由系統管理員定義) 本身可以在處理期間將宣告新增至連入證據。 在此案例中，簽發者會設定為 "AttestationPolicy"。
  - **CustomClaim**：證明方 (用戶端) 也可以將額外的宣告新增至證明證據。 在此案例中，簽發者會設定為 "CustomClaim"。

如果未定義， 預設值將會是 "CustomClaim"。

## <a name="claim-rule"></a>宣告規則

原則引擎會使用連入宣告集來計算證明結果。 宣告規則是一組用來驗證連入宣告並採取定義動作的條件。

```
Conditions list => Action (Claim)
```

宣告規則的 Azure 證明評估包含下列步驟：

- 如果條件清單不存在，使用指定的宣告來執行動作 
- 否則，從條件清單中評估條件。
- 如果條件清單評估為 false，則停止。 否則，則繼續。

宣告規則中的條件會用來判斷是否需要執行動作。 條件清單是以 "&&" 運算子分隔的一系列條件。

條件清單的結構如下：

```
Condition && Condition && ...
```

條件的結構如下：

```
Identifier:[ClaimPropertyCondition, ClaimPropertyCondition,…]
```

條件清單是由各種宣告屬性的個別條件所組成。 條件可以有選擇性的識別碼，可用來參考符合條件的宣告。 此參考可用於其他條件或相同規則的動作。

例如

```
F1:[type=="OSName" , issuer=="CustomClaim"] && 
[type=="OSName" , issuer=="AttestationService", value== F1.value ] 
=> issueproperty(type="report_validity_in_minutes", value=1440);

F1:[type=="OSName" , issuer=="CustomClaim"] && 
C2:[type=="OSName" , issuer=="AttestationService", value== F1.value ] 
=> issue(claim = C2);
```

以下是可以用來檢查條件的運算子：

| Valuetype | 支援的作業 |
|--|--|
| 整數 | == (等於)、\!= (不等於)、<= (小於或等於)、< (小於)、>= (大於或等於)、> (大於) |
| String | == (等於)、\!= (不等於) |
| Boolean | == (等於)、\!= (不等於) |

條件清單的評估：

- "&&" 運算子的存在意味著，只有在清單中所有條件都評估為 true 時，才會將條件清單評估為 true。
- 條件代表宣告集的篩選準則。 如果找到至少一個符合條件的宣告，則條件本身會評估為 true。
- 如果宣告的每個屬性都符合條件中出現的對應宣告屬性條件，則表示宣告滿足條件所表示的篩選準則。  

原則中所允許的動作集合如下所述。

| 動作的動詞命令 | 描述 | 適用的原則區段 |
|--|--|--|
| permit() | 連入宣告集可以用來計算 **issuancerules**。 不接受任何宣告作為參數 | **authorizationrules** |
| deny() | 連入宣告集不應用來計算 **issuancerules** 不接受任何宣告作為參數 | **authorizationrules** |
| add(claim) | 將宣告新增至連入宣告集。 新增到連入宣告集的任何宣告，都會提供給後續的宣告規則使用。 |**authorizationrules**、**issuancerules** |
| issue(claim) | 將宣告新增至連入和連出宣告集 | **issuancerules** |
| issueproperty(claim) | 將宣告新增至連入的和屬性宣告集 | **issuancerules**

## <a name="next-steps"></a>後續步驟

- [如何撰寫和簽署證明原則](author-sign-policy.md)
- [使用 PowerShell 設定 Azure 證明](quickstart-powershell.md)


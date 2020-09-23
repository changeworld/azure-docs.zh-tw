---
title: 瞭解 Azure 原則中的範圍
description: 描述 Azure Resource Manager 中的範圍概念，以及如何將它套用至 Azure 原則來控制 Azure 原則評估的資源。
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: 83eda330a35b007abfa37046a202b14728726849
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90984425"
---
# <a name="understand-scope-in-azure-policy"></a>瞭解 Azure 原則中的範圍

有許多設定會決定哪些資源能夠進行評估，以及 Azure 原則評估哪些資源。 這些控制項的主要概念是「 _範圍_」。
如需高階總覽，請參閱 [Azure Resource Manager 中的範圍](../../../azure-resource-manager/management/overview.md#understand-scope)。
本文說明 _範圍_ 在 Azure 原則中的影響，以及其相關的物件和屬性。

## <a name="definition-location"></a>定義位置

Azure 原則使用的第一個實例範圍是在建立原則定義時。 定義可以儲存在管理群組或訂用帳戶中。 位置決定可指派方案或原則的範圍。 資源必須在定義位置的資源階層內，以做為指派的目標。

如果定義位置為：

- 該訂用帳戶內的僅限**訂**用帳戶資源可指派原則定義給該訂用帳戶。
- 只有**管理群組**-子管理群組和子訂用帳戶內的資源可以指派原則定義。 如果您打算將原則定義套用至多個訂用帳戶，則該位置必須是包含每個訂用帳戶的管理群組。

位置應該是您想要使用原則定義的所有資源所共用的資源容器。 此資源容器通常是根管理群組附近的管理群組。

## <a name="assignment-scopes"></a>指派範圍

指派具有數個設定範圍的屬性。 使用這些屬性會決定要評估 Azure 原則的資源，以及要將哪些資源計入符合規範。 這些屬性會對應至下列概念：

- 包含：資源階層或個別資源應該依照定義來評估是否符合規範。 `properties.scope`指派物件上的屬性會決定要包含和評估合規性的內容。 如需詳細資訊，請參閱 [指派定義](./assignment-structure.md)。

- 排除-資源階層或個別資源不應依照定義進行合規性評估。 `properties.notScopes`指派物件上的_array_屬性會決定要排除的內容。 這些範圍中的資源不會評估或納入合規性計數中。 如需詳細資訊，請參閱 [指派定義-排除的範圍](./assignment-structure.md#excluded-scopes)。

除了原則指派上的屬性之外，也是 [原則豁免](./exemption-structure.md) 物件。 豁免會提供方法來識別要不評估之指派的一部分，藉此增強範圍案例。

- 豁免 (**預覽功能中的免費**) -資源階層或個別資源應依照定義進行評估，但不會評估是否有因應措施，例如有棄權或透過另一種方法降低的原因。 處於此狀態的資源會顯示為已 **豁免** 的合規性報告，以便進行追蹤。 豁免物件會建立在資源階層或個別資源上，以做為子物件，以決定豁免的範圍。 資源階層或個別資源可以豁免至多個指派。 您可以使用屬性將豁免設定為依排程到期 `expiresOn` 。 如需詳細資訊，請參閱 [豁免定義](./exemption-structure.md)。

  > [!NOTE]
  > 因為對資源階層或個別資源授與豁免的影響，所以豁免有額外的安全性措施。 除了在 `Microsoft.Authorization/policyExemptions/write` 資源階層或個別資源上要求作業之外，豁免的建立者必須在 `exempt/Action` 目標指派上擁有動詞。

## <a name="scope-comparison"></a>範圍比較

下表是範圍選項的比較：

| | 包含 | 排除 (notScopes)  | 豁免 |
|---|:---:|:---:|:---:|
|**評估資源** | &#10004; | - | - |
|**Resource Manager 物件** | - | - | &#10004; |
|**需要修改原則指派物件** | &#10004; | &#10004; | - |

## <a name="next-steps"></a>後續步驟

- 了解[原則定義結構](./definition-structure.md)。
- 了解如何[以程式設計方式建立原則](../how-to/programmatically-create.md)。
- 了解如何[取得合規性資料](../how-to/get-compliance-data.md)。
- 了解如何[補救不符合規範的資源](../how-to/remediate-resources.md)。
- 透過[使用 Azure 管理群組來組織資源](../../management-groups/overview.md)來檢閱何謂管理群組。
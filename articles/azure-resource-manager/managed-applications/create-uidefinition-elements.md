---
title: 建立 UI 定義元素
description: 描述在建構 Azure 入口網站的 UI 定義時要使用的元素。
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 221e4dc2e909561c70b1cd1b86df026619c2b31f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87004302"
---
# <a name="createuidefinition-elements"></a>CreateUiDefinition 元素

本文描述 CreateUiDefinition 所有支援元素的結構描述和屬性。 

## <a name="schema"></a>結構描述

大部分元素的結構描述如下所示︰

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Some text box",
  "defaultValue": "my value",
  "toolTip": "Provide a descriptive name.",
  "constraints": {},
  "options": {},
  "visible": true
}
```

| 屬性 | 必要 | 描述 |
| -------- | -------- | ----------- |
| NAME | 是 | 要參考元素特定執行個體的內部識別碼。 元素名稱的最常見用法是在 `outputs`，其中指定元素的輸出值會對應到範本的參數。 您也可以使用它，將元素的輸出值繫結至另一個元素的 `defaultValue`。 |
| 類型 | 是 | 要呈現元素的 UI 控制項。 如需支援類型的清單，請參閱[元素](#elements)。 |
| 標籤 | 是 | 元素的顯示文字。 某些元素類型會包含多個標籤，因此值可能是包含多個字串的物件。 |
| defaultValue | 否 | 元素的預設值。 某些元素類型支援複雜的預設值，因此值可能是物件。 |
| 工具提示 | 否 | 要顯示在元素之工具提示的文字。 類似於 `label`，某些元素可支援多個工具提示字串。 您可以使用 Markdown 語法將內嵌連結進行內嵌。
| 條件約束 | 否 | 用於自訂元素驗證行為的一個或多個屬性。 支援的條件約束屬性會依元素類型而有所不同。 某些元素類型不支援自訂驗證行為，因此沒有任何 constraints 屬性。 |
| 選項 | 否 | 自訂元素行為的其他屬性。 類似於 `constraints`，支援的屬性會依元素類型而有所不同。 |
| 可見 | 否 | 指出是否要顯示元素。 如果為 `true`，就會顯示元素和適用的子元素。 預設值是 `true`。 使用[邏輯函式](create-uidefinition-functions.md#logical-functions)以動態方式控制這個屬性的值。

## <a name="elements"></a>元素

每個元素的文件中包含元素行為的 UI 範例、結構描述、註解 (通常是關於驗證和支援的自訂) 以及範例輸出。

- [[一般] 核取方塊](microsoft-common-checkbox.md)
- [Microsoft.Common.DropDown](microsoft-common-dropdown.md)
- [Microsoft.Common.FileUpload](microsoft-common-fileupload.md)
- [Microsoft.Common.InfoBox](microsoft-common-infobox.md)
- [Microsoft.Common.OptionsGroup](microsoft-common-optionsgroup.md)
- [Microsoft.Common.PasswordBox](microsoft-common-passwordbox.md)
- [Microsoft.Common.Section](microsoft-common-section.md)
- [Microsoft 一般滑杆](microsoft-common-slider.md)
- [TagsByResource](microsoft-common-tagsbyresource.md)
- [Microsoft.Common.TextBlock](microsoft-common-textblock.md)
- [Microsoft.Common.TextBox](microsoft-common-textbox.md)
- [Microsoft.Compute.CredentialsCombo](microsoft-compute-credentialscombo.md)
- [Microsoft.Compute.SizeSelector](microsoft-compute-sizeselector.md)
- [Microsoft.Compute.UserNameTextBox](microsoft-compute-usernametextbox.md)
- [Microsoft.managedidentity. IdentitySelector](microsoft-managedidentity-identityselector.md)
- [Microsoft.Network.PublicIpAddressCombo](microsoft-network-publicipaddresscombo.md)
- [Microsoft.Network.VirtualNetworkCombo](microsoft-network-virtualnetworkcombo.md)
- [ArmApiControl](microsoft-solutions-armapicontrol.md)
- [ResourceSelector](microsoft-solutions-resourceselector.md)
- [Microsoft.Storage.MultiStorageAccountCombo](microsoft-storage-multistorageaccountcombo.md)
- [Microsoft.Storage.StorageAccountSelector](microsoft-storage-storageaccountselector.md)

## <a name="next-steps"></a>接下來的步驟

如需建立 UI 定義的簡介，請參閱[開始使用 CreateUiDefinition](create-uidefinition-overview.md)。

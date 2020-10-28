---
title: StorageBlobSelector UI 元素
description: 描述 Azure 入口網站的 StorageBlobSelector UI 元素。
author: tfitzmac
ms.topic: conceptual
ms.date: 10/27/2020
ms.author: tomfitz
ms.openlocfilehash: 1085b70df67a3f16a7f7f8c5ae85c9ab271b62ac
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92754419"
---
# <a name="microsoftstoragestorageblobselector-ui-element"></a>StorageBlobSelector UI 元素

從 Azure 儲存體帳戶選取 blob 的控制項。

## <a name="ui-sample"></a>UI 範例

使用者會看到流覽可用儲存體 blob 的選項。

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-browse.png" alt-text="StorageBlobSelector-流覽":::

選取 **[流覽]** 之後，使用者可以選取儲存體帳戶。

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-select.png" alt-text="StorageBlobSelector-流覽":::

使用者會看到儲存體帳戶中的容器，並可選取其中一個。

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-containers.png" alt-text="StorageBlobSelector-流覽":::

從容器中，使用者可以選取檔案。

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-file.png" alt-text="StorageBlobSelector-流覽":::

控制項會更新以顯示所選的檔案名。

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-result.png" alt-text="StorageBlobSelector-流覽":::

## <a name="schema"></a>結構描述

```json
{
  "name": "storageBlobSelection",
  "type": "Microsoft.Storage.StorageBlobSelector",
  "visible": true,
  "toolTip": "Select storage blob",
  "label": "Package (.zip, .cspkg)",
  "options": {
    "text": "Select Package"
  },
  "constraints": {
    "allowedFileExtensions": [ "zip", "cspkg" ]
  }
}
```

## <a name="sample-output"></a>範例輸出

```json
{
  "blobName": "test.zip",
  "sasUri": "https://azstorageaccnt1.blob.core.windows.net/container1/test.zip?sp=r&se=2020-10-10T07:46:22Z&sv=2019-12-12&sr=b&sig=X4EL8ZsRmiP1TVxkVfTcGyMj2sHg1zCbFBXsDmnNOyg%3D"
}

```

## <a name="remarks"></a>備註

**AllowedFileExtensions** 屬性會指定允許的檔案類型。

## <a name="next-steps"></a>後續步驟

* 如需建立 UI 定義的簡介，請參閱[開始使用 CreateUiDefinition](create-uidefinition-overview.md)。
* 如需 UI 元素中通用屬性的說明，請參閱 [CreateUiDefinition 元素](create-uidefinition-elements.md)。

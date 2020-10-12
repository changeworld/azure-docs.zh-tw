---
title: 安裝適用於 Unity 的遠端轉譯套件
description: 說明如何安裝 Unity 的遠端呈現用戶端 Dll
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 3704d1a418baeec18c3303b8203a0185790cbcc7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85564304"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>安裝適用於 Unity 的遠端轉譯套件

Azure 遠端轉譯使用 Unity 套件將整合封裝到 Unity。

## <a name="manage-the-remote-rendering-packages-in-unity"></a>管理 Unity 中的遠端呈現套件

Unity 套件是可透過 Unity 的 [封裝管理員](https://docs.unity3d.com/Packages/com.unity.package-manager-ui@1.8/manual/index.html)管理的容器。
此套件包含整個 c # API，以及搭配 Unity 使用 Azure 遠端轉譯所需的所有外掛程式二進位檔。
針對套件採用 Unity 的命名配置，套件稱為「 **.com. 遠端**轉譯」。

封裝不是 [ARR 範例存放庫](https://github.com/Azure/azure-remote-rendering)的一部分，而且無法從 Unity 的內部套件登錄中使用。 若要將它加入至專案，您必須手動編輯專案的檔案， `manifest.md` 以新增下列專案：

```json
{
  "scopedRegistries": [
    {
      "name": "Azure Mixed Reality Services",
      "url": "https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM/",
      "scopes": ["com.microsoft.azure"]
    }
   ],
  "dependencies": {
    "com.microsoft.azure.remote-rendering": "0.1.31",
    ...existing dependencies...
  }
}
```

新增之後，您可以使用 Unity 封裝管理員，以確保您擁有最新版本。
[教學課程： View Remote 模型](../../tutorials/unity/view-remote-models/view-remote-models.md)中提供更完整的指示。

## <a name="unity-render-pipelines"></a>Unity 轉譯管線

遠端轉譯適用于 **:::no-loc text="Universal render pipeline":::** 和 **:::no-loc text="Standard render pipeline":::** 。 基於效能考慮，建議使用通用轉譯管線。

若要使用 **:::no-loc text="Universal render pipeline":::** ，其套件必須安裝在 Unity 中。 您可以在 Unity 的 **封裝管理員** UI 中完成這項操作 (封裝名稱 **通用 RP**、7.3.1 版或更新版本的) ，或透過檔案 `Packages/manifest.json` （如 [Unity 專案設定教學](../../tutorials/unity/view-remote-models/view-remote-models.md#include-the-azure-remote-rendering-package)課程中所述）。

## <a name="next-steps"></a>接下來的步驟

* [Unity 遊戲物件和元件](objects-components.md)
* [教學課程：查看遠端模型](../../tutorials/unity/view-remote-models/view-remote-models.md)

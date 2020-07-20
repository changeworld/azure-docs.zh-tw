---
title: 安裝適用於 Unity 的遠端轉譯套件
description: 說明如何安裝 Unity 的遠端呈現用戶端 Dll
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 3704d1a418baeec18c3303b8203a0185790cbcc7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564304"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>安裝適用於 Unity 的遠端轉譯套件

Azure 遠端轉譯會使用 Unity 封裝將整合封裝到 Unity。

## <a name="manage-the-remote-rendering-packages-in-unity"></a>管理 Unity 中的遠端轉譯套件

Unity 封裝是可以透過 Unity 的[封裝管理員](https://docs.unity3d.com/Packages/com.unity.package-manager-ui@1.8/manual/index.html)來管理的容器。
此套件包含整個 c # API，以及搭配使用 Azure 遠端轉譯和 Unity 所需的所有外掛程式二進位檔。
遵循適用于封裝的 Unity 命名配置，此封裝稱為「 **com. azure 遠端呈現**」。

此封裝不是[ARR 範例存放庫](https://github.com/Azure/azure-remote-rendering)的一部分，而且無法從 Unity 的內部封裝登錄中使用。 若要將它加入至專案，您必須手動編輯專案的檔案， `manifest.md` 以加入下列內容：

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

新增之後，您就可以使用 Unity 封裝管理員來確保您擁有最新版本。
如需更完整的指示，請參閱[教學課程：觀看遠端模型](../../tutorials/unity/view-remote-models/view-remote-models.md)。

## <a name="unity-render-pipelines"></a>Unity 呈現管線

遠端轉譯同時適用于 **:::no-loc text="Universal render pipeline":::** 和 **:::no-loc text="Standard render pipeline":::** 。 基於效能考慮，建議使用通用呈現管線。

若要使用 **:::no-loc text="Universal render pipeline":::** ，其封裝必須安裝在 Unity 中。 這可以在 Unity 的**套件管理員**UI （套件名稱**通用 RP**、7.3.1 或更新版本）中完成，或透過檔案進行 `Packages/manifest.json` ，如[Unity 專案設定教學](../../tutorials/unity/view-remote-models/view-remote-models.md#include-the-azure-remote-rendering-package)課程中所述。

## <a name="next-steps"></a>後續步驟

* [Unity 遊戲物件和元件](objects-components.md)
* [教學課程：查看遠端模型](../../tutorials/unity/view-remote-models/view-remote-models.md)

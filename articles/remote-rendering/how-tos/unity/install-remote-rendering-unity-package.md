---
title: 安裝適用於 Unity 的遠端轉譯套件
description: 說明如何安裝 Unity 的遠端呈現用戶端 Dll
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 86ffe47c009f9e0ae121ffc6ab57bff8fb73210f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681177"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>安裝適用於 Unity 的遠端轉譯套件

Azure 遠端轉譯會使用 Unity 封裝將整合封裝到 Unity。

## <a name="manage-the-remote-rendering-packages-in-unity"></a>管理 Unity 中的遠端轉譯套件

Unity 封裝是可以透過 Unity 的[封裝管理員](https://docs.unity3d.com/Packages/com.unity.package-manager-ui@1.8/manual/index.html)來管理的容器。
此套件包含整個 c # API，以及搭配使用 Azure 遠端轉譯和 Unity 所需的所有外掛程式二進位檔。
遵循適用于封裝的 Unity 命名配置，此封裝稱為「 **com. azure 遠端呈現**」。

此封裝不是[ARR 範例存放庫](https://github.com/Azure/azure-remote-rendering)的一部分，而且無法從 Unity 的內部封裝登錄中使用。 若要將它加入至專案，您必須手動編輯專案的`manifest.md`檔案，以加入下列內容：
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
    "com.microsoft.azure.remote-rendering": "0.1.11",
    ...existing dependencies...
  }
}
```
新增之後，您就可以使用 Unity 封裝管理員來確保您擁有最新版本。
[教學課程：從頭開始設定 Unity 專案](../../tutorials/unity/project-setup.md)中提供更完整的指示。

## <a name="unity-render-pipelines"></a>Unity 呈現管線

遠端轉譯同時適用于**通用呈現管線**和標準轉譯**管線**。 基於效能考慮，建議使用通用呈現管線。

若要使用**通用呈現管線**，其套件必須安裝在 Unity 中。 這可以在 Unity 的**套件管理員**UI （套件名稱**通用 RP**、7.2.1 或更新版本）中完成，或透過`Packages/manifest.json`檔案進行，如[Unity 專案設定教學](../../tutorials/unity/project-setup.md#configure-the-projects-manifest)課程中所述。

## <a name="next-steps"></a>後續步驟

* [Unity 遊戲物件和元件](objects-components.md)
* [教學課程：從頭開始設定 Unity 專案](../../tutorials/unity/project-setup.md)

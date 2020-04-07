---
title: 安裝遠端呈現套件以進行統一
description: 說明如何安裝遠端的 DLL 以進行統一
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 86ffe47c009f9e0ae121ffc6ab57bff8fb73210f
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681177"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>安裝遠端呈現套件以進行統一

Azure 遠端呈現使用 Unity 包封裝到 Unity 中。

## <a name="manage-the-remote-rendering-packages-in-unity"></a>在 Unity 中管理遠端呈現套件

統一包是可以通過 Unity 的[包管理員](https://docs.unity3d.com/Packages/com.unity.package-manager-ui@1.8/manual/index.html)進行管理的容器。
此包包含整個 C# API 以及使用 Azure 遠端呈現與統一所需的所有外掛程式二進位檔案。
在 Unity 的套件命名方案之後,該包稱為**com.microsoft.azure.遠端呈現**。

該包不是[ARR 示例儲存庫](https://github.com/Azure/azure-remote-rendering)的一部分,並且從 Unity 的內部包註冊表中不可用。 要將新增到項目中,必須手動編輯項目`manifest.md`的檔案以新增以下內容:
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
添加此項後,可以使用 Unity 包管理器來確保擁有最新版本。
教學中給出了更全面的說明[:從頭開始設定統一專案](../../tutorials/unity/project-setup.md)。

## <a name="unity-render-pipelines"></a>整合圖管

遠端成成**一般的成一般的圖示與一般的****圖示**。 出於性能原因,建議使用通用渲染管道。

要使用**通用渲染管道**,其包必須安裝在 Unity 中。 這可以在 Unity 的**包管理員**UI(包名稱通用**RP、** 版本 7.2.1 或更新)中完成`Packages/manifest.json`,也可以通過檔案完成,如[Unity 專案設置教程](../../tutorials/unity/project-setup.md#configure-the-projects-manifest)中所述。

## <a name="next-steps"></a>後續步驟

* [整合一個元件與元件](objects-components.md)
* [教學:從頭開始設定 Unity 專案](../../tutorials/unity/project-setup.md)

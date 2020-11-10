---
title: Shell 轉譯
description: 說明如何使用 Shell 轉譯效果
author: jumeder
ms.author: jumeder
ms.date: 10/23/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: f59c4f8225d31b61df08f30863c8b9300e20e820
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94447620"
---
# <a name="shell-rendering"></a>Shell 轉譯

[階層式狀態覆寫元件](../../overview/features/override-hierarchical-state.md)的 shell 狀態為透明效果。 相對於 [查看](../../overview/features/override-hierarchical-state.md) 轉譯，只會顯示最上層的物件，類似于不透明轉譯。 此外，轉譯為 shell 時，物件的一般外觀可以改變。 此效果適用于使用者應以視覺化方式從非重要的部分引導，同時仍保有整個場景空間感知的使用案例。

您可以透過全域狀態設定 shell 轉譯物件的外觀 `ShellRenderingSettings` 。 使用 shell 轉譯的所有物件都將使用相同的設定。 每個物件參數都沒有。

## <a name="shellrenderingsettings-parameters"></a>ShellRenderingSettings 參數

類別會 `ShellRenderingSettings` 保存與全域 shell 轉譯屬性相關的設定：

| 參數      | 類型    | 說明                                             |
|----------------|---------|---------------------------------------------------------|
| `Desaturation` | FLOAT   | 要套用至一般最終物件色彩的 desaturation 量，範圍 0 (沒有 desaturation) 到 1 (完整 desaturation)  |
| `Opacity`      | FLOAT   | Shell 轉譯的物件不透明度，範圍 0 (不可見) 為 1 (完全不透明)  |

另請參閱下表，以取得套用至整個場景時的參數效果範例：

|                | 0 | 0.25 | 0.5 | 0.75 | 1.0 | 
|----------------|:-:|:----:|:---:|:----:|:---:|
| **Desaturation** | ![Desaturation-0。0](./media/shell-desaturation-00.png) | ![Desaturation-0.25](./media/shell-desaturation-025.png) | ![Desaturation-0。5](./media/shell-desaturation-05.png) | ![Desaturation-0.75](./media/shell-desaturation-075.png) | ![Desaturation-1。0](./media/shell-desaturation-10.png) |
| **不透明度**      | ![不透明度-0。0](./media/shell-opacity-00.png) | ![不透明度-0.25](./media/shell-opacity-025.png) | ![不透明度-0。5](./media/shell-opacity-05.png) | ![不透明度-0.75](./media/shell-opacity-075.png) | ![不透明度-1。0](./media/shell-opacity-10.png) |

Shell 效果會套用到最後的不透明色彩，否則場景會以其他方式呈現。 ，包含 [淡色階層式狀態覆寫](../../overview/features/override-hierarchical-state.md)。

## <a name="example"></a>範例

下列程式碼顯示透過 API 使用狀態的範例 `ShellRenderingSettings` ：

```cs
void SetShellSettings(AzureSession session)
{
    ShellRenderingSettings shellRenderingSettings = session.Actions.ShellRenderingSettings;
    shellRenderingSettings.Desaturation = 0.5f;
    shellRenderingSettings.Opacity = 0.1f;
}
```

```cpp
void SetShellSettings(ApiHandle<AzureSession> session)
{
    ApiHandle<ShellRenderingSettings> shellRenderingSettings = session->Actions()->GetShellRenderingSettings();
    shellRenderingSettings->SetDesaturation(0.5f);
    shellRenderingSettings->SetOpacity(0.1f);
}
```

## <a name="performance"></a>效能

Shell 轉譯功能與標準的不透明轉譯相較之下，會帶來少許的固定負荷。 這比使用物件上的透明材質或 [查看](../../overview/features/override-hierarchical-state.md) 轉譯的速度快很多。 如果只將部分場景切換至 shell 轉譯，效能可能會降低。 這種效能降低的原因，可能是因為另外需要呈現的物件需要轉譯。 在這方面，效能的行為類似于 [剪下平面](../../overview/features/cut-planes.md) 功能。

## <a name="next-steps"></a>後續步驟

* [階層式狀態覆寫元件](../../overview/features/override-hierarchical-state.md)
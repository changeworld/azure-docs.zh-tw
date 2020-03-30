---
title: 在聚居筆記本中安裝套裝軟體 - Azure 筆記本預覽
description: 瞭解如何在 Azure 上運行的 Jupyter 筆記本中安裝 Python、R 和 F# 包。
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: f6359b4c010834ffaee00b6208d309997339f36e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646223"
---
# <a name="install-packages-from-within-azure-notebooks-preview"></a>從 Azure 筆記本預覽中安裝包

雖然您可以設定[適用於專案層級上 Notebook 的環境](configure-manage-azure-notebooks-projects.md#configure-the-project-environment)，還是建議您直接在個別 Notebook 內安裝套件。

從 Notebook 安裝的套件只會套用至目前的伺服器工作階段。 一旦伺服器關閉，就不會保存套件安裝。

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="python"></a>Python

Python 中的套件可以使用 pip 或 conda，在程式碼資料格內使用命令來安裝：

```bash
!pip install <package_name>

!conda install <package_name> -y
```

如果命令輸出中表示已符合需求，則根據預設，Azure Notebooks 會包含套件。 套件也可以透過[專案環境設定步驟](configure-manage-azure-notebooks-projects.md#configure-the-project-environment)來安裝。

## <a name="r"></a>R

R 中的套件可以透過 CRAN 或 GitHub，在程式碼資料格中使用 `install.packages` 函式來安裝：

```r
install.packages("package_name")
```

您也可以從 GitHub 使用 devtools 程式庫，安裝發行前版本和其他開發套件：

```r
options(unzip = 'internal')
library(devtools)
install_github('<user>/<repo>')
```

## <a name="f"></a>F#

F# 中的套件可以藉由從程式碼資料格內呼叫 Paket 相依性管理員，從 [nuget.org](https://www.nuget.org) 安裝。 首先，載入 Paket 管理員：

```fsharp
#load "Paket.fsx"
```

然後安裝套件：

```fsharp
Paket.Package
  [ "MathNet.Numerics"
    "MathNet.Numerics.FSharp"
  ]
```

然後載入 Paket 產生器：
```fsharp
#load "Paket.Generated.Refs.fsx"
```

打開庫：
```fsharp
open MathNet.Numerics
```

## <a name="next-steps"></a>後續步驟

- [如何：配置和管理專案](configure-manage-azure-notebooks-projects.md)
- [如何：展示幻燈片](present-jupyter-notebooks-slideshow.md)

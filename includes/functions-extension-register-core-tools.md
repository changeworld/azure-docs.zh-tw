---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/07/2020
ms.author: glenga
ms.openlocfilehash: 2936d22eacef73daef4433b3fd296dd4757fa410
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88031113"
---
如果您無法使用延伸模組組合，您可以在本機使用 Azure Functions Core Tools，以安裝專案所需的特定延伸模組套件。 

> [!NOTE]
> 若要使用核心工具手動安裝延伸模組，您必須安裝 .NET Core 2.x SDK。

當您明確安裝延伸模組時，名為 extensions .csproj 的 .NET 專案檔會加入至專案的根目錄。 此檔案會定義您的函式所需的 NuGet 套件集合。 雖然您可以使用此檔案中的[NuGet 套件參考](/nuget/consume-packages/package-references-in-project-files)，但核心工具可讓您安裝延伸模組，而不需要手動編輯檔案。

有數種方式可以使用核心工具，在您的本機專案中安裝必要的延伸模組。 

#### <a name="install-all-extensions"></a>安裝所有延伸模組 

使用下列命令，在您的本機專案中自動新增系結所使用的所有延伸模組套件：

```dotnetcli
func extensions install
```
命令會讀取檔案*上的function.js* ，以查看您所需的套件、加以安裝，然後重建延伸模組專案 (副檔名 .csproj) 。 它會在目前版本中新增任何新繫結，但不會更新現有的繫結。 在安裝新的繫結時，使用 `--force` 選項將現有繫結更新為最新版本。

#### <a name="install-a-specific-extension"></a>安裝特定的擴充功能

使用下列命令來安裝特定版本的特定延伸模組套件，在此案例中為存放裝置延伸模組：

```dotnetcli
func extensions install --package Microsoft.Azure.WebJobs.Extensions.Storage --version 4.0.2
```
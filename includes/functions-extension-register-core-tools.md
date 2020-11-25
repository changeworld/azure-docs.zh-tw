---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/07/2020
ms.author: glenga
ms.openlocfilehash: a4f03223b5067d18f5d6e747b3bb630a86031b8f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020332"
---
如果您無法使用延伸模組套件組合，可以在本機使用 Azure Functions Core Tools 安裝專案所需的特定擴充套件。

> [!IMPORTANT]
> 您無法在使用擴充套件組合的函式應用程式中明確安裝擴充功能。 `extensionBundle`明確安裝擴充功能之前，請先移除 *host.js* 中的區段。

下列專案說明您可能需要手動安裝擴充功能的一些原因：

* 您需要存取套件組合中無法使用的特定延伸模組版本。
* 您需要存取組合中未提供的自訂擴充功能。
* 您需要存取單一套件組合中未提供的特定擴充功能組合。

> [!NOTE]
> 若要使用 Core Tools 手動安裝擴充功能，您必須安裝 [.Net Core 2.X SDK](https://dotnet.microsoft.com/download) 。 .NET Core SDK 是由 Azure Functions Core Tools 用來從 NuGet 安裝擴充功能。 您不需要知道 .NET 就能使用 Azure Functions 擴充功能。

當您明確地安裝擴充功能時，會將名為 extensions 的 .NET 專案檔新增至專案的根目錄。 此檔案會定義您的函式所需的一組 NuGet 套件。 雖然您可以在此檔案中使用 [NuGet 套件參考](/nuget/consume-packages/package-references-in-project-files) ，但 Core Tools 可讓您安裝延伸模組，而不需要手動編輯檔案。

有幾種方式可使用 Core Tools 在本機專案中安裝必要的延伸模組。 

#### <a name="install-all-extensions"></a>安裝所有延伸模組 

使用下列命令，在您的本機專案中自動新增系結所使用的所有擴充功能套件：

```dotnetcli
func extensions install
```
此命令會讀取檔案 *上的function.js* ，以查看您需要的套件、安裝它們，以及重建擴充功能專案 (副檔名 .csproj) 。 它會在目前版本中新增任何新繫結，但不會更新現有的繫結。 在安裝新的繫結時，使用 `--force` 選項將現有繫結更新為最新版本。

如果您的函式應用程式使用 Core Tools 無法辨識的系結，您必須手動安裝特定的擴充功能。

#### <a name="install-a-specific-extension"></a>安裝特定的擴充功能

使用下列命令，在特定版本（在此案例中為儲存體擴充功能）安裝特定的擴充套件：

```dotnetcli
func extensions install --package Microsoft.Azure.WebJobs.Extensions.Storage --version 4.0.2
```

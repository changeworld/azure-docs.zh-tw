---
title: Visual Studio Code 如何與 Azure Dev Spaces 搭配運作
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: conceptual
description: 瞭解 Visual Studio Code 和 Azure Dev Spaces 如何協助您進行 Kubernetes 應用程式的偵錯工具和快速反覆運算
keywords: Azure Dev Spaces、Dev Spaces、Docker、Kubernetes、Azure、AKS、Azure Kubernetes Service、容器
ms.openlocfilehash: decf61bd55ed15182f030bb02a2fe9c959749556
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88209873"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>Visual Studio Code 如何與 Azure Dev Spaces 搭配運作

您可以使用 Visual Studio Code 和 [Azure Dev Spaces 擴充][azds-extension] 功能，透過 Azure Dev Spaces 來準備、執行和偵測服務。 使用 Visual Studio Code 和 Azure Dev Spaces 擴充功能，您可以：

* 在 AKS 中產生用來執行和偵錯工具服務的資產
* 在開發人員空間中執行您的 JAVA、Node.js 和 .NET Core 服務
* 在開發人員空間中直接對您的 JAVA、Node.js 和 .NET Core 服務進行 debug 錯

## <a name="generate-assets"></a>產生資產

Visual Studio Code 和 Azure Dev Spaces 延伸模組會為您的專案產生下列資產：

* 使用 Maven、Node.js 應用程式和 .NET Core 應用程式的 JAVA 應用程式 dockerfile
* 使用 Dockerfile Helm 幾乎任何語言的圖表
* 檔案 `azds.yaml` ，也就是您專案的[Azure Dev Spaces 設定檔][azds-yaml]
* `.vscode`具有 Visual Studio Code 使用 Maven、Node.js 應用程式和 .Net Core 應用程式為 JAVA 應用程式啟動專案設定的資料夾

Dockerfile、Helm 圖表和檔案 `azds.yaml` 是執行時產生的相同資產 `azds prep` 。 這些檔案也可以在 Visual Studio 程式碼之外使用，以在 AKS 中執行您的專案，例如執行 `azds up` 。 `.vscode`只有 Visual Studio 程式碼使用此資料夾，才能從 Visual Studio Code 在 AKS 中執行您的專案。

## <a name="run-your-service-in-aks"></a>在 AKS 中執行您的服務

產生專案的資產之後，您可以從 Visual Studio Code 在現有的開發空間中執行 JAVA、Node.js 和 .NET Core 服務。 在 Visual Studio Code 的 [ *調試* 程式] 頁面上，您可以從目錄叫用啟動設定 `.vscode` 來執行專案。

您必須建立 AKS 叢集，並在 Visual Studio Code 以外的叢集中啟用 Azure Dev Spaces。 您可以重複使用現有的 Dockerfile、Helm 圖表，以及在 `azds.yaml` Visual Studio Code 之外建立的檔案，例如執行所產生的資產 `azds prep` 。 如果您重複使用 Visual Studio Code 以外產生的資產，您仍然需要有 `.vscode` 目錄。 `.vscode`您可以 Visual Studio 程式碼和 Azure Dev Spaces 擴充功能來重新產生此目錄，而不會覆寫您現有的資產。

針對 .NET Core 專案，您必須安裝 [c # 擴充][csharp-extension] 功能，才能從 Visual Studio Code 執行您的 .net 服務。 此外，針對使用 Maven 的 JAVA 專案，您必須安裝 [適用于 Azure Dev Spaces 擴充功能的 JAVA 偵錯工具][java-extension] ，以及 [安裝和設定的 Maven][maven] ，以從 Visual Studio Code 執行您的 java 服務。

## <a name="debug-your-service-in-aks"></a>在 AKS 中對您的服務進行 Debug

啟動您的專案之後，您可以直接從 Visual Studio Code，在開發空間中進行執行的 JAVA、Node.js 和 .NET Core 服務的偵錯工具。 目錄中的啟動設定會 `.vscode` 提供額外的偵錯工具資訊，以在開發人員空間中啟用啟用偵錯工具的服務。 Visual Studio Code 也會在開發人員空間中的執行中容器附加至偵錯工具，可讓您設定中斷點、檢查變數，以及執行其他的偵錯工具。

## <a name="next-steps"></a>後續步驟

深入了解 Azure Dev Spaces 的運作方式。

> [!div class="nextstepaction"]
> [Azure Dev Spaces 如何運作](how-dev-spaces-works.md)

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works-prep.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org

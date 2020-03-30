---
title: 視覺化工作室代碼如何與 Azure 開發空間配合使用
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: conceptual
description: 瞭解視覺化工作室代碼和 Azure 開發人員空間如何説明您調試和快速反覆運算 Kubernetes 應用程式
keywords: Azure 開發空間、開發空間、Docker、庫伯奈斯、Azure、AKS、Azure 庫伯奈斯服務、容器
ms.openlocfilehash: 91440e59fdb8c21579ef1f04e78e66f933221ba0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240452"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>視覺化工作室代碼如何與 Azure 開發空間配合使用

可以使用視覺化工作室代碼和 Azure[開發空間擴展][azds-extension]使用 Azure 開發空間準備、運行和調試服務。 借助視覺化工作室代碼和 Azure 開發空間擴展，您可以：

* 生成用於在 AKS 中運行和調試服務的資產
* 在開發空間中運行 JAVA、Node.js 和 .NET 核心服務
* 直接調試在開發空間中運行的 JAVA、Node.js 和 .NET Core 服務

## <a name="generate-assets"></a>生成資產

視覺化工作室代碼和 Azure 開發空間擴展為專案生成以下資源：

* 使用 Maven、Node.js 應用程式和 .NET Core 應用程式的 JAVA 應用程式的 Dockerfile
* 幾乎任何語言的頭盔圖表與 Dockerfile
* 檔`azds.yaml`，它是專案的[Azure 開發空間設定檔][azds-yaml]
* 具有`.vscode`使用 Maven、Node.js 應用程式和 .NET Core 應用程式的 JAVA 應用程式的專案的視覺化工作室代碼啟動配置的資料夾

Dockerfile、Helm 圖表和`azds.yaml`檔與運行時`azds prep`生成的資產相同。 這些檔還可用於 Visual Studio 代碼之外，以在 AKS 中運行專案，例如`azds up`運行 。 該`.vscode`資料夾僅由 Visual Studio 代碼使用，以從 Visual Studio 代碼在 AKS 中運行專案。

## <a name="run-your-service-in-aks"></a>在 AKS 中運行服務

為專案生成資源後，可以從 Visual Studio Code 在現有開發空間中運行 JAVA、Node.js 和 .NET Core 服務。 在 Visual Studio 代碼的*調試*頁中，可以從`.vscode`目錄中調用啟動配置來運行專案。

您必須創建 AKS 群集，並在視覺化工作室代碼之外的群集中啟用 Azure 開發空間。 例如，可以使用 Azure CLI 或 Azure 門戶執行此設置。 您可以重用現有的 Dockerfile、Helm 圖表和`azds.yaml`在 Visual Studio 代碼之外創建的檔，例如運行`azds prep`生成的資產。 如果重複使用在 Visual Studio 代碼之外生成的資產，則仍然需要有一個`.vscode`目錄。 此`.vscode`目錄可以通過 Visual Studio 代碼和 Azure 開發人員空間擴展重新生成，並且不會覆蓋現有資產。

對於 .NET Core 專案，必須安裝[C# 擴展][csharp-extension]才能從 Visual Studio 代碼運行 .NET 服務。 對於使用 Maven 的 JAVA 專案，必須安裝[Azure 開發人員空間擴展的 JAVA 調試器][java-extension]，以及[安裝 Maven 並將其配置為][maven]從 Visual Studio 代碼運行 JAVA 服務。

## <a name="debug-your-service-in-aks"></a>在 AKS 中調試您的服務

啟動專案後，可以直接從 Visual Studio Code 調試在開發空間中運行的 JAVA、Node.js 和 .NET Core 服務。 目錄中的`.vscode`啟動配置提供了用於在開發空間中啟用調試的服務運行服務的其他調試資訊。 Visual Studio Code 還附加到開發空間中正在運行的容器中的調試過程，允許您設置中斷點、檢查變數和執行其他調試操作。


## <a name="use-visual-studio-code-with-azure-dev-spaces"></a>將視覺化工作室代碼與 Azure 開發空間一起使用

您可以在以下快速入門中查看使用 Azure 開發空間的視覺化工作室代碼和 Azure 開發空間擴展：

* [使用視覺化工作室代碼和 JAVA 快速反覆運算和調試][quickstart-java]
* [使用視覺化工作室代碼和 .NET 快速反覆運算和調試][quickstart-netcore]
* [使用視覺化工作室代碼和 Node.js 快速反覆運算和調試][quickstart-node]

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works-prep.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md

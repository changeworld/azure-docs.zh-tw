---
title: 使用 Azure 開發人員空間遠端偵錯代碼的工作原理
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: 描述使用 Azure 開發人員空間在 Azure 庫伯內斯服務上進行遠端偵錯的過程
keywords: Azure 開發空間、開發空間、Docker、庫伯奈斯、Azure、AKS、Azure 庫伯奈斯服務、容器
ms.openlocfilehash: a9f303ea6f954285a319be137a594f7ce127f740
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241396"
---
# <a name="how-remote-debugging-your-code-with-azure-dev-spaces-works"></a>使用 Azure 開發人員空間遠端偵錯代碼的工作原理

Azure 開發人員空間為您提供了多種方法來快速反覆運算和調試 Kubernetes 應用程式，並與您的團隊協作處理 Azure Kubernetes 服務 （AKS） 群集。 專案在開發空間中運行後，Azure 開發人員空間提供了一種在 AKS 中附加和調試正在運行的應用程式的方法。

本文介紹了使用開發人員空間的遠端偵錯的工作原理。

## <a name="debug-your-code"></a>偵錯程式碼

對於 JAVA、.NET Core 和 Node.js 應用程式，您可以使用 Visual Studio 代碼或視覺化工作室調試直接在開發空間中運行的應用程式。 Visual Studio 代碼和視覺化工作室提供用於連接到開發空間、啟動應用程式和附加調試器的工具。 運行`azds prep`後，您可以在視覺化工作室代碼或視覺化工作室中打開專案。 視覺工作室代碼或視覺化工作室將生成自己的設定檔進行連接，這些檔與運行`azds prep`分開。 在 Visual Studio 代碼或視覺化工作室中，您可以設置中斷點並將應用程式啟動到開發空間。

![偵錯您的程式碼](media/get-started-node/debug-configuration-nodejs2.png)

當您使用 Visual Studio 代碼或 Visual Studio 進行調試時，它們會以與運行`azds up`相同的方式處理啟動和連接到開發空間。 此外，Visual Studio 代碼和 Visual Studio 中的用戶端工具都提供了一個附加參數，其中提供了用於調試的特定資訊。 該參數包含調試器映射的名稱、調試器在調試器映射中的位置以及應用程式容器中用於裝載調試器資料夾的目標位置。

調試器映射由用戶端工具自動確定。 它使用的方法類似于在運行`azds prep`時生成的 Dockerfile 和 Helm 圖表期間使用的方法。 調試器安裝在應用程式映射中後，使用`azds exec`運行調試器。

## <a name="next-steps"></a>後續步驟

要開始使用 Azure 開發人員空間進行遠端偵錯專案，請參閱以下快速入門：

* [使用視覺化工作室代碼和 JAVA 快速反覆運算和調試][quickstart-java]
* [使用視覺化工作室代碼和 .NET 快速反覆運算和調試][quickstart-netcore]
* [使用視覺化工作室代碼和 Node.js 快速反覆運算和調試][quickstart-node]
* [使用 Visual Studio 和 .NET 核心快速反覆運算和調試][quickstart-vs]


[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
[quickstart-vs]: quickstart-netcore-visualstudio.md

---
title: 如何使用 Azure Dev Spaces 進行遠端偵錯程式碼的運作方式
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: 描述在 Azure Kubernetes Service 上使用 Azure Dev Spaces 進行遠端偵錯程式的進程。
keywords: Azure Dev Spaces、Dev Spaces、Docker、Kubernetes、Azure、AKS、Azure Kubernetes Service、容器
ms.openlocfilehash: fd984ff6a8ebe336f76643406c0957769dbfd3da
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88213392"
---
# <a name="how-remote-debugging-your-code-with-azure-dev-spaces-works"></a>如何使用 Azure Dev Spaces 進行遠端偵錯程式碼的運作方式

Azure Dev Spaces 提供了多種方式，可供迅速逐一查看 Kubernetes 應用程式並偵錯，以及在 Azure Kubernetes Service (AKS) 叢集上與您的小組共同作業。 當您的專案在開發人員空間中執行之後，Azure Dev Spaces 提供一種方法，在 AKS 中附加至執行中的應用程式並加以調試。

本文說明如何使用 Dev Spaces 進行遠端偵錯。

## <a name="debug-your-code"></a>偵錯程式碼

針對 JAVA、.NET Core 和 Node.js 應用程式，您可以使用 Visual Studio Code 或 Visual Studio，在開發人員空間中直接對執行的應用程式進行 debug。 Visual Studio Code 和 Visual Studio 提供工具來連接到您的開發人員空間、啟動應用程式，以及附加偵錯工具。 執行之後 `azds prep` ，您可以在 Visual Studio Code 或 Visual Studio 中開啟專案。 Visual Studio Code 或 Visual Studio 會產生自己的設定檔來進行連線，這與執行的不同 `azds prep` 。 從 Visual Studio Code 或 Visual Studio 中，您可以設定中斷點，並啟動應用程式到您的開發人員空間。

![偵錯您的程式碼](media/get-started-node/debug-configuration-nodejs2.png)

當您使用 Visual Studio Code 或 Visual Studio 進行偵錯工具啟動應用程式時，它們會以執行的相同方式來處理啟動和連接到您的開發人員空間 `azds up` 。 此外，Visual Studio Code 和 Visual Studio 中的用戶端工具會提供額外的參數，其中包含用於進行偵錯工具的特定資訊。 參數包含偵錯工具映射的名稱、偵錯工具映射中的偵錯工具位置，以及應用程式容器內用來掛接偵錯工具資料夾的目的地位置。

偵錯工具映射是由用戶端工具自動決定。 它會使用類似于 Dockerfile 和 Helm 圖表在執行時所產生的方法 `azds prep` 。 偵錯工具裝載于應用程式的映射之後，就會使用來執行 `azds exec` 。

## <a name="next-steps"></a>後續步驟

深入瞭解 Azure Dev Spaces 的運作方式。

> [!div class="nextstepaction"]
> [Azure Dev Spaces 如何運作](how-dev-spaces-works.md)

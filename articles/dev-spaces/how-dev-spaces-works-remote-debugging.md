---
title: 如何使用 Azure Dev Spaces 在遠端偵錯程式碼的運作方式
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: 描述在 Azure Kubernetes Service 上進行遠端偵錯處理的程式 Azure Dev Spaces
keywords: Azure Dev Spaces、Dev Spaces、Docker、Kubernetes、Azure、AKS、Azure Kubernetes Service、容器
ms.openlocfilehash: fd984ff6a8ebe336f76643406c0957769dbfd3da
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88213392"
---
# <a name="how-remote-debugging-your-code-with-azure-dev-spaces-works"></a>如何使用 Azure Dev Spaces 在遠端偵錯程式碼的運作方式

Azure Dev Spaces 提供了多種方式，可供迅速逐一查看 Kubernetes 應用程式並偵錯，以及在 Azure Kubernetes Service (AKS) 叢集上與您的小組共同作業。 當您的專案在開發人員空間中執行時，Azure Dev Spaces 可讓您在 AKS 中附加至正在執行的應用程式並進行偵錯工具。

本文說明如何使用 Dev Spaces 進行遠端偵錯處理。

## <a name="debug-your-code"></a>偵錯程式碼

針對 JAVA、.NET Core 和 Node.js 的應用程式，您可以使用 Visual Studio Code 或 Visual Studio，在開發空間中直接對您的應用程式進行偵錯工具。 Visual Studio Code 和 Visual Studio 提供可連接到開發空間、啟動應用程式，以及附加偵錯工具的工具。 執行之後 `azds prep` ，您可以在 Visual Studio Code 或 Visual Studio 中開啟您的專案。 Visual Studio Code 或 Visual Studio 將會產生自己的設定檔來進行連接，而這與執行不同 `azds prep` 。 您可以從 Visual Studio Code 或 Visual Studio 中設定中斷點，並啟動應用程式至您的開發空間。

![偵錯您的程式碼](media/get-started-node/debug-configuration-nodejs2.png)

當您使用 Visual Studio Code 或 Visual Studio 來啟動應用程式以進行偵錯工具時，它們會以執行的相同方式來處理啟動和連接到您的開發空間 `azds up` 。 此外，Visual Studio Code 和 Visual Studio 中的用戶端工具會提供額外的參數，其中包含用於偵錯工具的特定資訊。 參數包含偵錯工具映射的名稱、偵錯工具的映射內的偵錯工具位置，以及應用程式容器內要掛接偵錯工具資料夾的目的地位置。

偵錯工具映射會由用戶端工具自動決定。 它使用的方法類似于在執行 Dockerfile 和 Helm 圖表時所使用的方法 `azds prep` 。 在應用程式的映射中裝載偵錯工具之後，就會使用來執行它 `azds exec` 。

## <a name="next-steps"></a>後續步驟

深入了解 Azure Dev Spaces 的運作方式。

> [!div class="nextstepaction"]
> [Azure Dev Spaces 如何運作](how-dev-spaces-works.md)

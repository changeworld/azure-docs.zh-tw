---
title: 建立自訂動作和資源
description: 本教學課程將探討如何在 Azure Resource Manager 中建立自訂動作和資源。 其中也會說明自訂工作流程如何與 Azure Resource Manager 範本、Azure CLI、Azure 原則和 Azure 活動記錄交互操作。
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 45f18727b53b802ba746da41b47fe955543ed9d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "75648545"
---
# <a name="create-custom-actions-and-resources-in-azure"></a>在 Azure 中建立自訂動作和資源

自訂提供者是 Azure 和端點之間的合約。 透過自訂提供者，您可以藉由將新的 API 新增至 Azure Resource Manager 來變更 Azure 中的工作流程。 透過這些自訂 API，Resource Manager 可以使用新的部署和管理功能。

本教學課程會詳細解說一個簡單範例，讓您了解如何將新的動作和資源新增至 Azure，以及如何進行整合。

## <a name="set-up-azure-functions-for-azure-custom-providers"></a>設定 Azure 自訂提供者的 Azure Functions

本教學課程的第一個部分將說明如何設定 Azure 函式應用程式以使用自訂提供者：

- [設定 Azure 自訂提供者的 Azure Functions](./tutorial-custom-providers-function-setup.md)

自訂提供者可以使用任何公用 URL。

## <a name="author-a-restful-endpoint-for-custom-providers"></a>撰寫自訂提供者的 RESTful 端點

本教學課程的第二部分將說明如何撰寫自訂提供者的 RESTful 端點：

- [撰寫自訂提供者的 RESTful 端點](./tutorial-custom-providers-function-authoring.md)

## <a name="create-and-use-a-custom-provider"></a>建立並使用自訂提供者

本教學課程的第三部分將說明如何建立自訂提供者，並使用其自訂動作和資源：

- [建立並使用自訂提供者](./tutorial-custom-providers-create.md)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解自訂提供者及其建置方式。 若要繼續下一個教學課程，請參閱[教學課程：設定 Azure 自訂提供者的 Azure Functions](./tutorial-custom-providers-function-setup.md)。

如果您要尋找參考或快速入門，以下有一些實用連結：

- [快速入門：建立 Azure 自訂資源提供者和部署自訂資源](./create-custom-provider.md)
- [操作說明：將自訂動作新增至 Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [操作說明：將自訂資源新增至 Azure REST API](./custom-providers-resources-endpoint-how-to.md)

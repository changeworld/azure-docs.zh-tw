---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/2/2019
ms.author: crtreasu
ms.openlocfilehash: 7e7825e8247e78cbc0c0e9e22bdbd9326939e0a8
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "83998047"
---
下一個步驟是將應用程式設定為使用您的帳戶識別碼和帳戶金鑰。 [設定空間錨點資源](#create-a-spatial-anchors-resource)時，將它們複製到文字編輯器中。

在 [專案] 窗格中瀏覽至 `Assets\AzureSpatialAnchors.SDK\Resources`。 選取 `SpatialAnchorConfig`。 然後，在 [偵測器] 窗格中，輸入 `Account Key` 做為 `Spatial Anchors Account Key` 的值，並輸入 `Account ID` 做為 `Spatial Anchors Account Id` 的值。

接下來，開啟 `SpatialAnchorManager.cs`。 找出 `CreateSessionAsync()` 並新增下列程式碼行，以替換先前帳戶網域中的項目：`session.Configuration.AccountDomain = "MyAccountDomain";`。 您可以在此註解 `// Configure authentication` 前直接新增這一行。

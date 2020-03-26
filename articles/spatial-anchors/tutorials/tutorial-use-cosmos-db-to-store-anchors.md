---
title: 教學課程：使用 Azure Cosmos DB 共用錨點
description: 在本教學課程中，您將了解如何透過後端服務和 Azure Cosmos DB 在 Unity 中跨 Android/iOS 裝置共用 Azure Spatial Anchors 識別碼。
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 71b3027d86400d6921895f86e257ddff2961f91f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "77615161"
---
# <a name="tutorial-sharing-azure-spatial-anchors-across-sessions-and-devices-with-an-azure-cosmos-db-back-end"></a>教學課程：跨工作階段和裝置與 Azure Cosmos DB 後端共用 Azure Spatial Anchors

本教學課程是[跨工作階段和裝置來共用 Azure Spatial Anchors](../../../articles/spatial-anchors/tutorials/tutorial-share-anchors-across-devices.md) 的後續。 本文會引導您完成新增幾項功能的流程，讓 Azure Cosmos DB 成為後端儲存體，同時在工作階段和裝置之間共用 Azure 空間錨點。

![說明物件持續性的 GIF](./media/persistence.gif)

應注意的是，雖然您在本教學課程中將使用 Unity 和 Azure Cosmos DB，但這只為了提供範例，說明如何跨裝置共用 Spatial Anchors 識別碼。 您可以使用其他語言和後端技術來達到相同的目標。 此外，本教學課程所使用的 ASP.NET Core Web 應用程式需要 .NET Core 2.2 SDK。 它在「適用於 Windows 的 Web 應用程式」上可正常執行，但目前無法在「適用於 Linux 的 Web 應用程式」上執行。

## <a name="create-a-database-account"></a>建立資料庫帳戶

將 Azure Cosmos 資料庫新增至您稍早建立的資源群組。

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

複製 `Connection String` 因為您將需要它。

## <a name="make-minor-changes-to-the-sharingservice-files"></a>對 SharingService 檔案進行小幅變更

在 [方案總管]  中開啟 `SharingService\Startup.cs`。

找出檔案頂端的 `#define INMEMORY_DEMO`，並將它註解化。儲存檔案。

在 [方案總管]  中開啟 `SharingService\appsettings.json`。

找出 `StorageConnectionString` 屬性，並將其值設為您在[建立資料庫帳戶步驟](#create-a-database-account)中記下的 `Connection String` 值。 儲存檔案。

您可以再次發佈共用服務，並執行範例應用程式。

## <a name="troubleshooting"></a>疑難排解

### <a name="unity-20193"></a>Unity 2019.3

由於有重大變更，目前不支援 Unity 2019.3。 請使用 Unity 2019.1 或 2019.2。

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已使用 Azure Cosmos DB 在各裝置間共用錨點識別碼。 若要深入了解如何在新的 Unity HoloLens 應用程式中使用 Azure Spatial Anchors，請繼續進行下一個教學課程。

> [!div class="nextstepaction"]
> [啟動新的 Unity HoloLens 應用程式](./tutorial-new-unity-hololens-app.md)

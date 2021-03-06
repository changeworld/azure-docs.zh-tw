---
title: 教學課程 - 清除資源 | Azure
description: 在本教學課程中，您會了解如何清除在建立 Web 應用程式時所配置的 Azure 資源。
services: storage, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 10/27/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: bf03fe9d920298d49e79a9a0febf7e09e94eb6ff
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905514"
---
# <a name="tutorial-clean-up-resources"></a>教學課程：清除資源

如果您已完成此多部分教學課程中的所有步驟，就會在資源群組中建立應用程式服務、應用程式服務主控方案和儲存體帳戶。 您也會在 Azure Active Directory 中建立應用程式註冊。 當不再需要這些資源和應用程式註冊時，請加以刪除，才不會繼續累積費用。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> * 刪除遵循教學課程時所建立的 Azure 資源。

## <a name="delete-the-resource-group"></a>刪除資源群組

在 [Azure 入口網站](https://portal.azure.com)中，從入口網站功能表選取 [資源群組]，然後選取包含您應用程式服務和應用程式服務方案的資源群組。

選取 [刪除資源群組] 來刪除群組及所有資源。

:::image type="content" alt-text="顯示刪除資源群組的螢幕擷取畫面。" source="./media/scenario-secure-app-clean-up-resources/delete-resource-group.png":::

此命令可能需要幾分鐘的時間來執行。

## <a name="delete-the-app-registration"></a>刪除應用程式註冊

從入口網站功能表中，選取 [Azure Active Directory] > [應用程式註冊]。 然後選取您建立的應用程式。
:::image type="content" alt-text="顯示選取應用程式註冊的螢幕擷取畫面。" source="./media/scenario-secure-app-clean-up-resources/select-app-registration.png":::

在應用程式註冊概觀中，選取 [刪除]。
:::image type="content" alt-text="顯示刪除應用程式註冊的螢幕擷取畫面。" source="./media/scenario-secure-app-clean-up-resources/delete-app-registration.png":::

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
>
> * 刪除遵循教學課程時所建立的 Azure 資源。

了解如何將 [.NET Core 應用程式](tutorial-dotnetcore-sqldb-app.md)、[Python 應用程式](tutorial-python-postgresql-app.md)、[JAVA 應用程式](tutorial-java-spring-cosmosdb.md)或 [Node.js 應用程式](tutorial-nodejs-mongodb-app.md)連線到資料庫。
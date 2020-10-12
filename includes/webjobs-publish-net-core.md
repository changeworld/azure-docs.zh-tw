---
title: 包含檔案
description: 包含檔案
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 06/26/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: e208b52c67f173bd0d289715b63562df656b1ec9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88009738"
---
1. 在 [方案總管] 中，以滑鼠右鍵按一下專案並選取 [發佈]。

1. 在 [**發行**] 對話方塊中，選取 [適用于**目標**的**Azure** ]，然後選取 **[下一步]**。 

1. 選取 [**特定目標** **Azure WebJobs** ]，然後選取 **[下一步]**。

1. 選取 [ **建立新的 Azure WebJob**]。

   ![挑選發佈目標](./media/webjobs-publish-netcore/pick-publish-target.png)

1. 在 [ **App Service (Windows) ** ] 對話方塊中，使用下表中的 [裝載設定]。

    | 設定      | 建議的值  | 描述                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **名稱** | 全域唯一的名稱 | 用以唯一識別新函式應用程式的名稱。 |
    | **訂用帳戶** | 選擇您的訂用帳戶 | 要使用的 Azure 訂用帳戶。 |
    | **[資源群組](../articles/azure-resource-manager/management/overview.md)** | myResourceGroup |  要在其中建立函式應用程式的資源群組名稱。 選擇 [新增]**** 以建立新的資源群組。|
    | **[主控方案](../articles/app-service/overview-hosting-plans.md)** | App Service 方案 | [App Service 方案](../articles/app-service/overview-hosting-plans.md)會指定用來裝載應用程式的 Web 伺服器陣列位置、大小和功能。 在裝載多個應用程式時，您可以將 Web 應用程式設定為共用單一 App Service 方案來節省開支。 App Service 方案定義區域、實例大小、小數位數和 SKU (免費、共用、基本、標準或 Premium) 。 選擇 [ **新增** ] 以建立新的 App Service 方案。 |

    ![建立 App Service 對話方塊](./media/webjobs-publish-netcore/app-service-dialog.png)

1. 選取 [ **建立** ] 以使用這些設定在 Azure 中建立 WebJob 和相關資源，並部署您的專案程式碼。
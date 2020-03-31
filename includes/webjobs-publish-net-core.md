---
title: 包含檔案
description: 包含檔案
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 02/19/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 4860532e59227618ce819772887556719ecb53fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76021177"
---
1. 在 [方案總管]**** 中，以滑鼠右鍵按一下專案並選取 [發佈]****。

1. 在 **"發佈"** 對話方塊中，選擇**Microsoft Azure 應用服務**，選擇 **"新建**"，然後選擇 **"發佈**"。

   ![選取發佈目標](./media/webjobs-publish-netcore/pick-publish-target.png)

1. 在 **"創建應用服務"** 對話方塊中，使用圖像下表中指定的託管設置：

    ![建立 App Service 對話方塊](./media/webjobs-publish-netcore/app-service-dialog.png)

    | 設定      | 建議的值  | 描述                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **應用名稱** | 全域唯一的名稱 | 用以唯一識別新函式應用程式的名稱。 |
    | **訂閱** | 選擇您的訂用帳戶 | 要使用的 Azure 訂用帳戶。 |
    | **[資源組](../articles/azure-resource-manager/management/overview.md)** | myResourceGroup |  要在其中建立函式應用程式的資源群組名稱。 選擇 [新增]**** 以建立新的資源群組。|
    | **[託管計畫](../articles/app-service/overview-hosting-plans.md)** | App Service 方案 | [應用服務方案](../articles/app-service/overview-hosting-plans.md)指定託管應用的 Web 服務器場的位置、大小和功能。 在裝載多個應用程式時，您可以將 Web 應用程式設定為共用單一 App Service 方案來節省開支。 應用服務方案定義區域、實例大小、規模計數和 SKU（免費、共用、基本、標準或高級）。 選擇 **"新建**"可創建新的應用服務方案。 |

1. 按一下 **"創建**"可使用這些設置在 Azure 中創建 WebJob 和相關資源並部署專案代碼。
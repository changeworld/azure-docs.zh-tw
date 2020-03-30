---
title: 使用 Azure 門戶部署服務目錄應用
description: 示範受控應用程式的取用者如何透過 Azure 入口網站來部署服務目錄應用程式。
author: tfitzmac
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: 9a69296ddfc93fd7e8a6650df91876829631f5d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473059"
---
# <a name="quickstart-deploy-service-catalog-app-through-azure-portal"></a>快速入門：通過 Azure 門戶部署服務目錄應用

在[前一個快速入門](publish-managed-app-definition-quickstart.md)中，您已發行受控應用程式定義。 在此快速入門中，您可以從該定義建立服務目錄應用程式。

## <a name="create-service-catalog-app"></a>建立服務目錄應用程式

在 Azure 入口網站中，使用下列步驟：

1. 選擇 **"創建資源**"。

   ![建立資源](./media/deploy-service-catalog-quickstart/create-new.png)

1. 搜尋**服務目錄受控應用程式**，然後從可用的選項中選取它。

   ![搜尋服務目錄應用程式](./media/deploy-service-catalog-quickstart/select-service-catalog.png)

1. 您會看到受控應用程式服務的說明。 選取 [建立]****。

   ![選取 [建立]](./media/deploy-service-catalog-quickstart/create-service-catalog.png)

1. 入口網站會顯示您有權存取的受控應用程式定義。 從可用的定義中，選取您想要部署的定義。 在此快速入門中，使用您在前一個快速入門中建立的**受控儲存體帳戶**定義。 選取 [建立]****。

   ![選取要部署的定義](./media/deploy-service-catalog-quickstart/select-definition.png)

1. 為 **"基本"** 選項卡提供值。選擇 Azure 訂閱以將服務目錄應用部署到 。 建立名為 **applicationGroup** 的新資源群組。 選取應用程式的位置。 完成後，選取 [確定]****。

   ![提供基本的值](./media/deploy-service-catalog-quickstart/provide-basics.png)

1. 提供儲存體帳戶名稱的前置詞。 選取要建立的儲存體帳戶類型。 完成後，選取 [確定]****。

   ![提供儲存體的值](./media/deploy-service-catalog-quickstart/provide-storage.png)

1. 檢閱摘要。 驗證成功之後，選取 [確定]**** 以開始部署。

   ![檢視摘要](./media/deploy-service-catalog-quickstart/view-summary.png)

## <a name="view-results"></a>檢視結果

在部署服務目錄應用程式之後，您會有兩個新的資源群組。 一個資源群組會保存服務目錄應用程式。 另一個資源群組則會保存服務目錄應用程式的資源。

1. 檢視名為 **applicationGroup** 的資源群組以查看服務目錄應用程式。

   ![檢視應用程式](./media/deploy-service-catalog-quickstart/view-managed-application.png)

1. 檢視名為 **applicationGroup{hash-characters}** 的資源群組以查看服務目錄應用程式的資源。

   ![檢視資源](./media/deploy-service-catalog-quickstart/view-resources.png)

## <a name="next-steps"></a>後續步驟

* 若要了解如何建立受控應用程式的定義檔，請參閱[建立及發行受控應用程式定義](publish-service-catalog-app.md)。
* 針對 Azure CLI，請參閱[使用 Azure CLI 部署服務目錄應用程式](./scripts/managed-application-cli-sample-create-application.md)。
* 針對 PowerShell，請參閱[使用 PowerShell 部署服務目錄應用程式](./scripts/managed-application-poweshell-sample-create-application.md)。

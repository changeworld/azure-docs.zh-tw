---
title: Azure Functions 的持續部署
description: 使用 Azure App Service 的持續部署功能來發佈您的函式。
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.topic: conceptual
ms.date: 09/25/2019
ms.openlocfilehash: e49c235e11eea17fdd1a7ff7751cc0493934d725
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "83123653"
---
# <a name="continuous-deployment-for-azure-functions"></a>Azure Functions 的持續部署

您可以使用 Azure Functions，藉由使用[原始檔控制整合](functions-deployment-technologies.md#source-control)，持續部署您的程式碼。 原始檔控制整合可讓程式碼更新觸發部署至 Azure 的工作流程。 如果您不熟悉 Azure Functions，請查看[Azure Functions 總覽](functions-overview.md)開始使用。

對於您整合多個經常參與的專案而言，持續部署是個不錯的選項。 當您使用持續部署時，您可以為程式碼維護單一真實來源，讓小組可以輕鬆地共同作業。 您可以在 Azure Functions 中，從下列原始程式碼位置設定持續部署：

* [Azure Repos](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [Bitbucket](https://bitbucket.org/)

Azure 中的函式部署單位是函式應用程式。 函數應用程式中的所有函式都會同時部署。 啟用連續部署之後，會將 Azure 入口網站中的函式程式碼存取設定為*唯讀*，因為事實的來源設定為在其他地方。

## <a name="requirements-for-continuous-deployment"></a>持續部署的需求

若要讓持續部署成功，您的目錄結構必須與 Azure Functions 預期的基本資料夾結構相容。

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

>[!NOTE]  
> 在取用方案上執行的 Linux 應用程式尚不支援持續部署。 

## <a name="set-up-continuous-deployment"></a><a name="credentials"></a>設定持續部署

若要設定現有函數應用程式的持續部署，請完成下列步驟。 這些步驟會示範與 GitHub 存放庫的整合，但類似的步驟適用于 Azure Repos 或其他原始程式碼儲存機制。

1. 在[Azure 入口網站](https://portal.azure.com)的函數應用程式中，選取 [**部署中心**]，選取 [ **GitHub**]，然後選取 [**授權**]。 如果您已授權 GitHub，請選取 [**繼續**]，然後略過下一個步驟。 

    :::image type="content" source="./media/functions-continuous-deployment/github.png" alt-text="Azure App Service 部署中心":::

3. 在 GitHub 中，選取 [**授權 AzureAppService**]。

    :::image type="content" source="./media/functions-continuous-deployment/authorize.png" alt-text="授權 Azure App Service":::

    輸入您的 GitHub 密碼，然後選取 [**繼續**]。

4. 選取下列其中一個組建提供者：

    * **App Service 組建服務**：當您不需要組建，或需要一般組建時，最佳做法。
    * **Azure Pipelines （預覽）**：當您需要更充分掌控組建時的最佳選擇。 此提供者目前為預覽狀態。

    選取 [繼續]。

5. 針對您指定的原始檔控制選項，設定特定的資訊。 針對 GitHub，您必須輸入或選取 [**組織**]、[存放**庫**] 和 [**分支**] 的值。 這些值是以您的程式碼位置為基礎。 然後選取 [**繼續**]。

    :::image type="content" source="./media/functions-continuous-deployment/github-specifics.png" alt-text="設定 GitHub":::

6. 查看所有詳細資料，然後選取 **[完成]** 以完成部署設定。

當程式完成時，來自指定來源的所有程式碼都會部署到您的應用程式。 此時，部署來源中的變更會觸發將這些變更部署至 Azure 中的函數應用程式。

> [!NOTE]
> 設定持續整合之後，您就無法再于函式入口網站中編輯原始程式檔。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Azure Functions 的最佳做法](functions-best-practices.md)

---
title: 從雲端資料夾同步內容
description: 瞭解如何透過從雲端資料夾（包括 OneDrive 或 Dropbox）的內容同步處理，將您的應用程式部署至 Azure App Service。
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.topic: article
ms.date: 12/03/2018
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 9f18eef607a5f655aecc0b6ebe1c85a239b34192
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77482967"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>將雲端資料夾的內容同步處理到 Azure App Service
本文說明如何將您的內容從 Dropbox 和 OneDrive 同步處理到 [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)。 

隨選內容同步處理部署是由 App Service 的 [Kudu 部署引擎](https://github.com/projectkudu/kudu/wiki) 所提供。 您可以使用您的應用程式程式碼和指定之雲端資料夾中的內容，然後按一下按鈕來同步處理 App Service。 內容同步處理會使用 Kudu 組建伺服器。 

## <a name="enable-content-sync-deployment"></a>啟用內容同步處理部署

若要啟用內容同步處理，請巡覽至 [Azure 入口網站](https://portal.azure.com)中的 App Service 應用程式頁面。

在左側功能表中，按一下 [**部署中心** > ] [**OneDrive** ] 或 [ **Dropbox** > **授權**]。 請遵循授權提示。 

![](media/app-service-deploy-content-sync/choose-source.png)

您只需要對 OneDrive 或 Dropbox 授權一次。 如果您已獲授權，只需按一下 [繼續]**** 即可。 您可以按一下 [變更帳戶]**** 來變更授權的 OneDrive 或 Dropbox 帳戶。

![](media/app-service-deploy-content-sync/continue.png)

在 [設定]**** 頁面上，選取您想要同步處理的資料夾。 這個資料夾就會在 OneDrive 或 Dropbox 的下列指定內容路徑下建立。 
   
* **OneDrive**：`Apps\Azure Web Apps`
* **Dropbox**：`Apps\Azure`

完成後，按一下 [繼續]****。

在 [摘要]**** 頁面上確認您的選項，然後按一下 [完成]****。

## <a name="synchronize-content"></a>同步處理內容

當您想要使用 App Service 同步處理雲端資料夾中的內容時，請回到 [部署中心]**** 頁面，然後按一下 [同步]****。

![](media/app-service-deploy-content-sync/synchronize.png)
   
   > [!NOTE]
   > 由於 API 中的基礎差異，目前不支援**商務用 OneDrive**。 
   > 
   > 

## <a name="disable-content-sync-deployment"></a>停用內容同步處理部署

若要停用內容同步處理，請巡覽至 [Azure 入口網站](https://portal.azure.com)中的 App Service 應用程式頁面。

在左側功能表中，按一下 [**部署中心** > **] [中斷連線]**。

![](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [從本機 Git 存放庫部署](deploy-local-git.md)

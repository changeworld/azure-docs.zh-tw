---
author: msftradford
ms.service: spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 80685dee7907b81832c94044d1feb8fcf2e41bde
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185278"
---
### <a name="open-the-publish-wizard"></a>開啟發佈精靈

在 [方案總管] 中，以滑鼠右鍵按一下 **SharingService** 專案，然後選取 [發佈]。

發佈精靈隨即啟動。 

選取 [App Service] > [發佈] 以開啟 [建立 App Service] 窗格。

### <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站。

在 [建立 App Service] 窗格上選取 [新增帳戶]，然後登入您的 Azure 訂用帳戶。 如果已經登入，請從下拉式清單中選取要使用的帳戶。

   > [!NOTE]
   > 如果您已經登入，請勿選取 [建立]。
   >

### <a name="create-a-resource-group"></a>建立資源群組

[!INCLUDE [resource group intro text](resource-group.md)]

選取 [資源群組] 旁邊的 [新增]。

將資源群組命名為 **myResourceGroup**，然後選取 [確定]。

### <a name="create-an-app-service-plan"></a>建立應用程式服務方案

[!INCLUDE [app-service-plan](app-service-plan.md)]

選取 [主控方案]旁的 [新增]。

在 [設定主控方案] 窗格中，使用以下設定：

| 設定 | 建議的值 | 描述 |
|-|-|-|
|App Service 方案| MySharingServicePlan | App Service 方案的名稱 |
| Location | 美國西部 | 裝載 Web 應用程式的資料中心 |
| 大小 | 免費 | 決定裝載功能的[定價層](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) |

選取 [確定]  。

### <a name="create-and-publish-the-web-app"></a>建立和發佈 Web 應用程式

在 [應用程式名稱] 中，輸入唯一的應用程式名稱。 有效的字元為 a-z、0-9 和破折號 (-)，或接受自動產生的唯一名稱。 Web 應用程式的 URL 是 `https://<app_name>.azurewebsites.net`，其中 `<app_name>` 是您的應用程式名稱。

選取 [建立] 開始建立 Azure 資源。

   精靈完成後，系統會將 ASP.NET Core Web 應用程式發佈至 Azure，然後在預設瀏覽器中開啟該應用程式。

  ![Azure 中已發佈的 ASP.NET Web 應用程式的螢幕擷取畫面。](./media/spatial-anchors-azure/web-app-running-live.png)

您在本區段中使用的應用程式名稱會被用來作為 URL 首碼，其格式為 `https://<app_name>.azurewebsites.net`。 將此 URL 複製到文字編輯器以供後續使用。

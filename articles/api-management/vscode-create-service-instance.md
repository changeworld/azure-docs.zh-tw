---
title: 使用 Visual Studio Code 建立 Azure API 管理執行個體 | Microsoft Docs
description: Visual Studio Code 可建立 Azure API 管理執行個體。
ms.service: api-management
ms.workload: integration
author: vladvino
ms.author: apimpm
ms.topic: quickstart
ms.date: 09/14/2020
ms.openlocfilehash: 19080679291b88b693c95bd71f8ddc0e59286356
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2020
ms.locfileid: "90057322"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-using-visual-studio-code"></a>快速入門：使用 Visual Studio Code 建立新的 Azure API 管理服務執行個體

「Azure API 管理 (APIM)」可協助組織將 API 發佈給外部、合作夥伴及內部開發人員，以發揮其資料與服務的潛力。 「API 管理」提供的核心專長認證，透過開發人員參與、商務洞察力、分析、安全性和保護，可確保 API 程式獲致成功。 APIM 可讓您為裝載於任何位置的現有後端服務，建立和管理新式 API 閘道。 如需詳細資訊，請參閱[概觀](api-management-key-concepts.md)主題。

本快速入門說明使用適用於 Visual Studio Code 的 *Azure API 管理擴充功能預覽版*來建立新 API 管理執行個體的步驟。 您也可以使用此擴充功能在您的 API 管理執行個體上執行常見的管理作業。

## <a name="prerequisites"></a>先決條件：

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

此外，請確定您已安裝下列各項：

- [Visual Studio Code](https://code.visualstudio.com/)

- [適用於 Visual Studio Code 的 Azure API 管理擴充功能 (預覽)](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement&ssr=false#overview)

## <a name="sign-in-to-azure"></a>登入 Azure

啟動 Visual Studio Code，然後開啟 Azure 擴充功能。 (如果您在活動列上沒看到 Azure 圖示，請確定您已啟用 *Azure API 管理*擴充功能。)

選取 [登入 Azure...] 以啟動瀏覽器視窗，並登入您的 Microsoft 帳戶。

![從適用於 VS Code 的 API 管理擴充功能登入 Azure](./media/vscode-create-service-instance/vscode-apim-login.png)

## <a name="create-an-api-management-service"></a>建立 API 管理服務

登入您的 Microsoft 帳戶之後，[Azure:API 管理] 總管窗格會列出您的 Azure 訂用帳戶。

以滑鼠右鍵按一下您想要使用的訂用帳戶，然後選取 [在 Azure中建立 API 管理]。

![在 VS Code 中建立 API 管理精靈](./media/vscode-create-service-instance/vscode-apim-create.png)

在開啟的窗格中提供新 API 管理執行個體的名稱。 此名稱在 Azure 內必須是全域唯一的，且包含1-50 個英數字元和 (或) 連字號，並以字母開頭、以英數字元結尾。

系統會使用指定的名稱建立新的 API 管理執行個體 (和父代資源群組)。 根據預設，執行個體會建立在*美國西部*區域，並使用*耗用量* SKU。

> [!TIP]
> 如果您在 [Azure API 管理擴充功能設定] 中啟用 [進階建立]，則也可以指定 [[API 管理 SKU](https://azure.microsoft.com/pricing/details/api-management/)]、[[Azure 區域](https://status.azure.com/en-us/status)] 和 [[資源群組](../azure-resource-manager/management/overview.md)] 來部署您的 API 管理執行個體。
>
> 雖然*耗用量* SKU 只需不到一分鐘的時間就能佈建好，但其他 SKU 一般需要30-40 分鐘的時間才能建立好。

至此，您已準備好匯入和發佈您的第一個 API。 您可以在適用於 Visual Studio Code 的擴充功能內執行此作業，並且還可以執行常見的 API 管理作業。 如需詳細資訊，請參閱[適用於 Visual Studio Code 的 API 管理擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement&ssr=false#overview)文件。

![VS Code API 管理擴充功能窗格中新建立的 API 管理執行個體](./media/vscode-create-service-instance/vscode-apim-instance.png)

## <a name="clean-up-resources"></a>清除資源

如果不再需要，請移除 API 管理執行個體，方法是以滑鼠右鍵按一下，然後選取 [在入口網站中開啟] 以 [[刪除 API 管理服務](get-started-create-service-instance.md#clean-up-resources)] 及其資源群組。

或者，您也可以選取 [刪除 API 管理]，從而只刪除 API 管理執行個體 (此作業不會刪除其資源群組)。

![從 VS Code 刪除 API 管理執行個體](./media/vscode-create-service-instance/vscode-apim-delete.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [匯入和發佈您的第一個 API](import-and-publish.md)

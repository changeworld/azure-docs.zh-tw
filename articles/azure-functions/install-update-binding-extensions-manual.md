---
title: 手動安裝或更新 Azure Functions 繫結延伸模組
description: 了解如何為已部署的函數應用程式安裝或更新 Azure Functions 繫結延伸模組。
ms.topic: reference
ms.date: 09/26/2018
ms.openlocfilehash: e8716f691a5d19ddac7fece47c423e1f7787b9db
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75768855"
---
# <a name="manually-install-or-update-azure-functions-binding-extensions-from-the-portal"></a>從入口網站手動安裝或更新 Azure Functions 繫結延伸模組

從2.x 版開始，Azure Functions 執行時間會使用系結延伸模組來執行觸發程式和系結的程式碼。 繫結延伸模組是在 NuGet 套件中提供的。 若要註冊延伸模組，您基本上會安裝套件。 開發函式時，安裝繫結延伸模組的方式取決於開發環境。 如需詳細資訊，請參閱觸發程序和繫結文章中的[註冊繫結延伸模組](./functions-bindings-register.md)。

有時，您需要在 Azure 入口網站中手動安裝或更新繫結延伸模組。 例如，您可能需要將某個已註冊的繫結更新至較新的版本。 您可能也需要在入口網站的 [整合]**** 索引標籤中，註冊某個已支援但無法安裝的繫結。

## <a name="install-a-binding-extension"></a>安裝繫結延伸模組

請使用下列步驟，從入口網站手動安裝或更新延伸模組。

1. 在 [Azure 入口網站](https://portal.azure.com)中，找出您的函數應用程式並選取它。 選擇 [概觀]**** 索引標籤，然後選取 [停止]****。  停止函數應用程式會將檔案解除鎖定，以便進行變更。

1. 選擇 [平台功能]**** 索引標籤，然後在 [開發工具]**** 底下，選取 [進階工具 (Kudu)]****。 Kudu 端點（`https://<APP_NAME>.scm.azurewebsites.net/`）會在新視窗中開啟。

1. 在 [Kudu] 視窗中，選取 [ **Debug console** > **CMD**]。  

1. 在命令視窗中，瀏覽至 `D:\home\site\wwwroot`，然後選擇 `bin` 旁邊的刪除圖示以刪除此資料夾。 選取 **[確定]** 以確認刪除。

1. 選擇 `extensions.csproj` 檔案旁邊的編輯圖示，此檔案定義了函數應用程式的繫結延伸模組。 專案檔會在線上編輯器中開啟。

1. 針對 **ItemGroup** 中的 **PackageReference** 項目進行必要的新增和更新，然後選取 [儲存]****。 您可以在[我需要什麼套件？](https://github.com/Azure/azure-functions-host/wiki/Updating-your-function-app-extensions#what-nuget-packages-do-i-need) \(英文\) Wiki 文章中，找到目前支援的套件版本清單。 所有三個「Azure 儲存體」繫結都需要 Microsoft.Azure.WebJobs.Extensions.Storage 套件。

1. 從 `wwwroot` 資料夾中，執行下列命令以在 `bin` 資料夾中重建所參考的組件。

    ```cmd
    dotnet build extensions.csproj -o bin --no-incremental --packages D:\home\.nuget
    ```

1. 返回入口網站中的 [概觀]**** 索引標籤，選擇 [啟動]**** 以重新啟動函數應用程式。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [深入了解 Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)

---
title: 如何設定 Azure Functions 執行階段目標版本
description: Azure Functions 支援多個執行階段版本。 了解如何指定 Azure 中裝載之函式應用程式的執行階段版本。
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: a7d86ef26d50d60389ae09bf3245ed97fea2c3e3
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88926570"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>如何設定 Azure Functions 執行階段目標版本

函式應用程式可在特定版本的 Azure Functions 執行階段中執行。 有三個主要版本： 1.x [、2.x 和](functions-versions.md)3.x。 根據預設，函式應用程式會建立在執行時間3.x 版中。 本文說明如何在 Azure 中設定要在您選擇的版本上執行的函式應用程式。 如需如何為特定版本設定本機開發環境的相關資訊，請參閱[在本機進行 Azure Functions 的程式碼編寫和測試](functions-run-local.md)。

## <a name="automatic-and-manual-version-updates"></a>自動和手動版本更新

Azure Functions 可讓您使用函式 `FUNCTIONS_EXTENSION_VERSION` 應用程式中的應用程式設定，將目標設為特定版本的執行時間。 函式應用程式會保留在指定的主要版本上，直到您明確選擇移至新版本為止。 如果您只指定主要版本，則函式應用程式會在執行時間變成可用時，自動更新為新的次要版本。 新的次要版本不應引進重大變更。 

如果您指定次要版本 (例如 "2.0.12345")，則函式應用程式會釘選到該特定版本，直到您明確地變更它。 較舊的次要版本會定期從生產環境中移除。 發生這種情況之後，您的函數應用程式會在最新版本上執行，而不是在中設定的版本 `FUNCTIONS_EXTENSION_VERSION` 。 因此，您應該快速解決需要特定次要版本的函式應用程式的任何問題，如此您就可以改為以主要版本為目標。 [App Service 宣告](https://github.com/Azure/app-service-announcements/issues)中會宣佈次要版本移除。

> [!NOTE]
> 如果您釘選到特定的 Azure Functions 主要版本，然後嘗試使用 Visual Studio 發佈至 Azure，則會出現對話方塊視窗提示您更新為最新版本，或取消發行。 若要避免這種情況，請 `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` 在您的檔案中新增屬性 `.csproj` 。

有新版本公開發行時，入口網站會提示您向上移至該版本。 移至新版本之後，您隨時可以使用 `FUNCTIONS_EXTENSION_VERSION` 應用程式設定移回舊版。

下表顯示 `FUNCTIONS_EXTENSION_VERSION` 每個主要版本的值，以啟用自動更新：

| 主要版本 | `FUNCTIONS_EXTENSION_VERSION` 值 |
| ------------- | ----------------------------------- |
| 3.x  | `~3` |
| 2.x  | `~2` |
| 1.x  | `~1` |

變更執行階段版本會導致函式應用程式重新啟動。

## <a name="view-and-update-the-current-runtime-version"></a>檢視並更新目前的執行階段版本

您可以變更函數應用程式所使用的執行階段版本。 由於可能會發生重大變更，因此您只能在函數應用程式中建立任何函式之前變更執行階段版本。 

> [!IMPORTANT]
> 雖然執行階段版本取決於 `FUNCTIONS_EXTENSION_VERSION` 設定，但您應該在 Azure 入口網站中進行這項變更，而不是直接變更該設定。 這是因為入口網站會驗證您的變更，並視需要進行其他相關變更。

# <a name="portal"></a>[入口網站](#tab/portal)

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> 使用 Azure 入口網站，您無法變更已包含函式之函式應用程式的執行階段版本。

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

您也可以從 Azure CLI 檢視並設定 `FUNCTIONS_EXTENSION_VERSION`。  

在 Azure CLI 中，使用 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) 命令檢視目前的執行階段版本。

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

在此程式碼中，以您的函式應用程式名稱取代 `<function_app>`。 還要以函式應用程式的資源群組名稱取代 `<my_resource_group>`。 

您會在下列輸出中看到 `FUNCTIONS_EXTENSION_VERSION`，為求清楚，已將輸出截斷：

```output
[
  {
    "name": "FUNCTIONS_EXTENSION_VERSION",
    "slotSetting": false,
    "value": "~2"
  },
  {
    "name": "FUNCTIONS_WORKER_RUNTIME",
    "slotSetting": false,
    "value": "dotnet"
  },
  
  ...
  
  {
    "name": "WEBSITE_NODE_DEFAULT_VERSION",
    "slotSetting": false,
    "value": "8.11.1"
  }
]
```

您可以使用 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) 命令，在函式應用程式中更新 `FUNCTIONS_EXTENSION_VERSION` 設定。

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--settings FUNCTIONS_EXTENSION_VERSION=<VERSION>
```

以函式應用程式的名稱取代 `<FUNCTION_APP>`。 還要以函式應用程式的資源群組名稱取代 `<RESOURCE_GROUP>`。 此外，請 `<VERSION>` 將取代為特定版本，或是 `~3` 、或 `~2` `~1` 。

您可以選擇上述程式碼範例中的 [試試看]****，從 [Azure Cloud Shell](../cloud-shell/overview.md) 執行此命令。 在執行 [az login](/cli/azure/reference-index#az-login) 登入之後，您也可以使用[本機 Azure CLI](/cli/azure/install-azure-cli) 來執行此命令。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要檢查 Azure Functions 執行時間，請使用下列 Cmdlet： 

```powershell
Get-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>"
```

以您的函式 `<FUNCTION_APP>` 應用程式名稱取代 `<RESOURCE_GROUP>` 。 此設定的目前值 `FUNCTIONS_EXTENSION_VERSION` 會在雜湊表中傳回。

使用下列腳本來變更函數執行時間：

```powershell
Update-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>" -AppSetting @{"FUNCTIONS_EXTENSION_VERSION" = "<VERSION>"} -Force
```

同樣地，以 `<FUNCTION_APP>` 您的函數應用程式名稱取代，並以 `<RESOURCE_GROUP>` 資源群組的名稱取代。 此外，請 `<VERSION>` 以特定版本或主要版本取代，例如 `~2` 或 `~3` 。 您可以 `FUNCTIONS_EXTENSION_VERSION` 在傳回的雜湊表中驗證設定的更新值。 

---

函數應用程式會在對應用程式設定進行變更之後重新開機。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [在本機開發環境中鎖定 2.0 執行階段](functions-run-local.md)

> [!div class="nextstepaction"]
> [請參閱執行階段版本的版本資訊](https://github.com/Azure/azure-webjobs-sdk-script/releases)

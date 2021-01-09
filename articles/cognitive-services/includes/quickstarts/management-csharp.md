---
title: 快速入門：適用於 .NET 的 Azure 管理用戶端程式庫
description: 在本快速入門中，開始使用適用於 .NET 的 Azure 管理用戶端程式庫。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 74f9ed5913ca089e12d5958fe96eeb1552c9137a
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/23/2020
ms.locfileid: "97745692"
---
[參考文件](/dotnet/api/overview/azure/cognitiveservices/management?view=azure-dotnet) | [程式庫來源程式碼](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Microsoft.Azure.Management.CognitiveServices) | [套件 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Management.CognitiveServices/) | [範例](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Microsoft.Azure.Management.CognitiveServices/tests)

## <a name="c-prerequisites"></a>C# 必要條件

* 有效的 Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)。
* 最新版 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)。

[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-c-application"></a>建立新的 C# 應用程式

建立新的 .NET Core 應用程式。 在主控台視窗中 (例如 cmd、PowerShell 或 Bash)，使用 `dotnet new` 命令建立名為 `azure-management-quickstart` 的新主控台應用程式。 此命令會建立簡單的 "Hello World" C# 專案，內含單一原始程式檔：*program.cs*。 

```console
dotnet new console -n azure-management-quickstart
```

將目錄變更為新建立的應用程式資料夾。 您可以使用下列命令來建置應用程式：

```console
dotnet build
```

建置輸出應該不會有警告或錯誤。 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>安裝用戶端程式庫

在應用程式目錄中，使用下列命令安裝適用於 .NET 的 Azure 管理用戶端程式庫：

```console
dotnet add package Microsoft.Azure.Management.CognitiveServices
dotnet add package Microsoft.Azure.Management.Fluent
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

如果您使用 Visual Studio IDE，則可以取得可下載 NuGet 套件形式的用戶端程式庫。

### <a name="import-libraries"></a>匯入程式庫

開啟 Program.cs，並將下列 `using` 陳述式新增至檔案的最上方：

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_using)]

## <a name="authenticate-the-client"></a>驗證用戶端

將下列欄位新增至 program.cs 的根目錄，並使用您所建立的服務主體和您的 Azure 帳戶資訊填入其值。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_constants)]

然後，在您的 **Main** 方法中，使用這些值來建構 **CognitiveServicesManagementClient** 物件。 您所有的 Azure 管理作業都需要此物件。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_assigns)]

## <a name="call-management-methods"></a>呼叫管理方法

將下列程式碼新增至您的 **Main** 方法，以列出可用的資源、建立範例資源、列出您擁有的資源，然後刪除範例資源。 您會在稍後的步驟中定義這些方法。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_calls)]

## <a name="create-a-cognitive-services-resource"></a>建立認知服務資源

若要建立並訂閱新的認知服務資源，請使用 **Create** 方法。 這個方法會將新的可計費資源新增至您傳入的資源群組。 建立新的資源時，必須知道您要使用的服務「種類」，以及其定價層 (或 SKU) 和 Azure 位置。 下列方法會將這些項目全部當作引數，並建立資源。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_create)]

### <a name="choose-a-service-and-pricing-tier"></a>選擇服務和定價層

建立新的資源時，必須知道您要使用的服務「種類」，以及想要的[定價層](https://azure.microsoft.com/pricing/details/cognitive-services/) (或 SKU)。 建立資源時，您將使用此資訊和其他資訊作為參數。 您可以在指令碼中呼叫下列方法，以尋找可用的認知服務「種類」清單：

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="view-your-resources"></a>檢視資源

若要檢視您 Azure 帳戶下的所有資源 (跨所有資源群組)，請使用下列方法：

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_list)]

## <a name="delete-a-resource"></a>刪除資源

下列方法會從給定的資源群組中刪除指定的資源。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_delete)]

## <a name="run-the-application"></a>執行應用程式

使用 `dotnet run` 命令從您的應用程式目錄執行應用程式。

```dotnet
dotnet run
```

## <a name="see-also"></a>另請參閱

* [Azure 管理 SDK 參考文件](/dotnet/api/overview/azure/cognitiveservices/management?view=azure-dotnet)
* [Azure 認知服務是什麼？](../../what-are-cognitive-services.md)
* [驗證 Azure 認知服務要求](../../authentication.md)
* [使用 Azure 入口網站建立新的資源](../../cognitive-services-apis-create-account.md)
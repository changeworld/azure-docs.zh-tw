---
title: 設定 Azure Functions
description: 本教學課程將介紹如何建立 Azure 函式應用程式，並將其設定為使用 Azure 自訂提供者
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 55554678047faeedd16b78dea61a42d50fd59491
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737315"
---
# <a name="set-up-azure-functions-for-azure-custom-providers"></a>設定 Azure 自訂提供者的 Azure Functions

自訂提供者是 Azure 和端點之間的合約。 透過自訂提供者，您可以在 Azure 中變更工作流程。 本教學課程將說明如何設定 Azure 函式應用程式以作為自訂提供者端點使用。

## <a name="create-the-azure-function-app"></a>建立 Azure 函式應用程式

> [!NOTE]
> 在本教學課程中，您會建立使用 Azure 函式應用程式的簡單服務端點。 不過，自訂提供者可以使用任何可公開存取的端點。 替代方案包括 Azure Logic Apps、Azure APIM，以及 Azure App Service 的 Web Apps 功能。

若要開始本教學課程，請先遵循[在 Azure 入口網站中建立您的第一個 Azure 函式](../../azure-functions/functions-get-started.md)教學課程。 該教學課程會建立可在 Azure 入口網站中修改的 .NET Core Webhook 函式。 這也是目前教學課程的基礎。

## <a name="install-azure-table-storage-bindings"></a>安裝 Azure 資料表儲存體繫結

若要安裝 Azure 資料表儲存體繫結：

1. 前往至 HttpTrigger 的 [整合]  索引標籤。
1. 選取 [+ 新增輸入]  。
1. 選取 [Azure 資料表儲存體]  。
1. 安裝 Microsoft.Azure.WebJobs.Extensions.Storage 擴充功能 (如果尚未安裝)。
1. 在 [資料表參數名稱]  方塊中，輸入 **tableStorage**。
1. 在 [資料表名稱]  方塊中，輸入 **myCustomResources**。
1. 選取 [儲存]  以儲存更新後的輸入參數。

![顯示資料表繫結的自訂提供者概觀](./media/create-custom-provider/azure-functions-table-bindings.png)

## <a name="update-restful-http-methods"></a>更新 RESTful HTTP 方法

若要設定 Azure 函式以包含自訂提供者的 RESTful 要求方法：

1. 前往至 HttpTrigger 的 [整合]  索引標籤。
1. 在 [選取的 HTTP 方法]  底下，選取 [GET]  、[POST]  、[DELETE]  和 [PUT]  。

![顯示 HTTP 方法的自訂提供者概觀](./media/create-custom-provider/azure-functions-http-methods.png)

## <a name="add-azure-resource-manager-nuget-packages"></a>新增 Azure Resource Manager NuGet 套件

> [!NOTE]
> 如果專案目錄中遺漏 C# 專案檔，您可以手動將其加入。 或者，當函式應用程式上安裝 Microsoft.Azure.WebJobs.Extensions.Storage 擴充功能之後，該檔案就會出現。

接下來，更新 C# 專案檔以包含實用的 NuGet 程式庫。 這些程式庫可讓您更輕鬆地剖析來自自訂提供者的傳入要求。 請遵循[從入口網站新增擴充功能](../../azure-functions/functions-bindings-register.md)中的步驟，並更新 C# 專案檔以納入下列套件參考：

```xml
<PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
<PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
<PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
```

下列 XML 元素是 C# 專案檔範例：

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFramework>netstandard2.0</TargetFramework>
    <WarningsAsErrors />
  </PropertyGroup>
  <ItemGroup>
    <PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
    <PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
    <PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
  </ItemGroup>
</Project>
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已設定 Azure 函式應用程式以作為 Azure 自訂提供者端點使用。

若要了解如何撰寫 RESTful 自訂提供者端點，請參閱[教學課程：撰寫 RESTful 自訂提供者端點](./tutorial-custom-providers-function-authoring.md)。
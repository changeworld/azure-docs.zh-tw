---
ms.openlocfilehash: b1be5e903994f0a2c4a3d457f0c2bb5572a889c3
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98663024"
---
## <a name="add-secret-manager"></a>新增祕密管理員

名為祕密管理員的工具能儲存專案樹狀結構外開發工作的敏感性資料。 此作法能協助避免於原始程式碼內意外共用應用程式祕密。 請完成下列步驟，以啟用在 ASP.NET Core 專案中使用秘密管理員：

#### <a name="net-5x"></a>[.NET 5。x](#tab/core5x)

瀏覽至專案的根目錄，然後執行下列命令以啟用專案中的秘密儲存體：

```dotnetcli
dotnet user-secrets init
```

包含 GUID 的 `UserSecretsId` 元素會新增至 .csproj 檔案：

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    
    <PropertyGroup>
        <TargetFramework>net5.0</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

</Project>
```

#### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

瀏覽至專案的根目錄，然後執行下列命令以啟用專案中的秘密儲存體：

```dotnetcli
dotnet user-secrets init
```

包含 GUID 的 `UserSecretsId` 元素會新增至 .csproj 檔案：

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    
    <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

</Project>
```

#### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

1. 開啟 .csproj  檔案。

1. 將 `UserSecretsId` 元素新增至 .csproj 檔案，如下所示。 您可以使用相同的 GUID，也可以將此值取代為您自己的值。

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
    
        <PropertyGroup>
            <TargetFramework>netcoreapp2.1</TargetFramework>
            <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
        </PropertyGroup>
    
        <ItemGroup>
            <PackageReference Include="Microsoft.AspNetCore.App" />
            <PackageReference Include="Microsoft.AspNetCore.Razor.Design" Version="2.1.2" PrivateAssets="All" />
        </ItemGroup>
    
    </Project>
    ```
    
1. 儲存 .csproj  檔案。

---

> [!TIP]
> 若要深入了解秘密管理員，請參閱[在 ASP.NET Core 中的開發中安全儲存應用程式秘密](/aspnet/core/security/app-secrets)。

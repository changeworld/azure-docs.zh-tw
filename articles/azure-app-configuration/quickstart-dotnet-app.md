---
title: Azure 應用程式設定搭配 .NET Framework 的快速入門 | Microsoft Docs
description: 搭配使用 Azure 應用程式設定與 .NET Framework 應用程式的快速入門
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: lcozzens
ms.openlocfilehash: cfa89a7921751541d1044d697237946cd63cbfd8
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/03/2020
ms.locfileid: "82732048"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>快速入門：使用 Azure 應用程式設定建立 .NET Framework 應用程式

在本快速入門中，您會將 Azure 應用程式組態納入 .NET Framework 型主控台應用程式中，以集中儲存和管理應用程式設定 (與您的程式碼分開)。

## <a name="prerequisites"></a>Prerequisites

- Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.2](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>建立應用程式組態存放區

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. 選取 [組態總管]   > [建立]   > [索引鍵/值]  以新增下列索引鍵/值組：

    | Key | 值 |
    |---|---|
    | TestApp:Settings:Message | Azure 應用程式設定的值 |

    目前先讓 [標籤]  和 [內容類型]  保持空白。

7. 選取 [套用]  。

## <a name="create-a-net-console-app"></a>建立 .NET 主控台應用程式

1. 啟動 Visual Studio，然後選取 [檔案]   > [新增]   > [專案]  。

1. 在 [建立新專案]  中，依照 [主控台]  專案類型篩選，然後按一下 [主控台應用程式 (.NET Framework)]  。 選取 [下一步]  。

1. 在 [設定您的新專案]  中，輸入專案名稱。 在 [架構]  底下，選取 [.NET Framework 4.7.1]  或更高版本。 選取 [建立]  。

## <a name="connect-to-an-app-configuration-store"></a>連線至應用程式組態存放區

1. 以滑鼠右鍵按一下專案，然後選取 [管理 NuGet 套件]  。 在 [瀏覽]  索引標籤上，搜尋下列 NuGet 套件並新增至您的專案。

    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 or later
    System.Configuration.ConfigurationManager version 4.6.0 or later
    ```

1. 更新您專案的 *App.config* 檔案，如下所示：

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" connectionString="${ConnectionString}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="ConnectionString" value ="Set via an environment variable - for example, dev, test, staging, or production connection string." />
    </appSettings>
    ```

   從環境變數 `ConnectionString` 中讀取應用程式組態存放區的連接字串。 在 `appSettings` 區段的 `configBuilders` 屬性中，於 `MyConfigStore` 之前新增 `Environment` 設定建立器。

1. 開啟 *Program.cs*，並藉由呼叫 `ConfigurationManager` 將 `Main` 方法更新為使用應用程式設定。

    ```csharp
    static void Main(string[] args)
    {
        string message = System.Configuration.ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
    }
    ```

## <a name="build-and-run-the-app-locally"></a>於本機建置並執行應用程式

1. 將名為 **ConnectionString** 的環境變數設定為應用程式組態存放區的連接字串。 如果您使用 Windows 命令提示字元，請執行下列命令：

    ```cmd
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    如果您使用 Windows PowerShell，請執行下列命令：

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```
1. 重新啟動 Visual Studio，以讓變更生效。 按 Ctrl + F5 以建置並執行主控台應用程式。

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立新的應用程式組態存放區，並將其與 .NET Framework 主控台應用程式搭配使用。 `ConfigurationManager` 的值 `AppSettings` 在應用程式啟動之後不會變更。 不過，應用程式組態 .NET Standard 組態提供者程式庫也可以在 .NET Framework 應用程式中使用。 若要了解如何讓 .NET Framework 應用程式以動態方式重新整理組態設定，請繼續進行下一個教學課程。

> [!div class="nextstepaction"]
> [啟用動態組態](./enable-dynamic-configuration-dotnet.md)

---
title: 使用 .NET SDK 將資料大量匯入至 Azure Cosmos DB SQL API 帳戶
description: 了解如何藉由建置會將匯入資料所需的佈建輸送量 (RU/秒) 最佳化的 .NET 主控台應用程式，來將資料匯入或擷取至 Azure Cosmos DB
author: ealsur
ms.author: maquaran
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 09/21/2020
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: cfab8ba88c7da84efb3f6aed6f95bb100507f8da
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90981909"
---
# <a name="bulk-import-data-to-azure-cosmos-db-sql-api-account-by-using-the-net-sdk"></a>使用 .NET SDK 將資料大量匯入至 Azure Cosmos DB SQL API 帳戶

本教學課程說明如何建立 .NET 主控台應用程式，以將資料匯入至 Azure Cosmos DB 所需的佈建輸送量 (RU/秒) 最佳化。 在本文中，您將從範例資料來源讀取資料，並將它匯入至 Azure Cosmos 容器。
本教學課程使用 [3.0+ 版](https://www.nuget.org/packages/Microsoft.Azure.Cosmos)的 Azure Cosmos DB .NET SDK，其以 .NET Framework 或 .NET Core 作為目標。

本教學課程涵蓋下列項目：

> [!div class="checklist"]
> * 建立 Azure Cosmos 帳戶
> * 設定您的專案
> * 連線至已啟用大量支援的 Azure Cosmos 帳戶
> * 透過並行建立作業執行資料匯入

## <a name="prerequisites"></a>Prerequisites

依照本文的指示進行之前，請確定您具備下列資源：

* 使用中的 Azure 帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [NET Core 3 SDK](https://dotnet.microsoft.com/download/dotnet-core). 您可以藉由執行 `dotnet --version`，來確認您環境中可使用的版本。

## <a name="step-1-create-an-azure-cosmos-db-account"></a>步驟 1:建立 Azure Cosmos DB 帳戶

從 Azure 入口網站[建立 Azure Cosmos DB SQL API 帳戶](create-cosmosdb-resources-portal.md)，或者您可以使用 [Azure Cosmos DB 模擬器](local-emulator.md)來建立帳戶。

## <a name="step-2-set-up-your-net-project"></a>步驟 2:設定 .NET 專案

從您的本機電腦開啟 Windows 命令提示字元或終端機視窗。 您將會從命令提示字元或終端機執行下一節中的所有命令。 執行下列 dotnet 新命令，來建立名為 *bulk-import-demo* 的新應用程式。 `--langVersion` 參數會設定所建立專案檔中的 *LangVersion* 屬性。

   ```bash
   dotnet new console –langVersion:8 -n bulk-import-demo
   ```

將目錄變更為新建立的應用程式資料夾。 您可以使用下列命令來建置應用程式：

   ```bash
   cd bulk-import-demo
   dotnet build
   ```

組建的預期輸出看起來應像這樣：

   ```bash
   Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\bulk-import-demo\bulk-import-demo.csproj.
     bulk -> C:\Users\user1\Downloads\CosmosDB_Samples\bulk-import-demo \bin\Debug\netcoreapp2.2\bulk-import-demo.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:34.17
   ```

## <a name="step-3-add-the-azure-cosmos-db-package"></a>步驟 3：新增 Azure Cosmos DB 封裝

若您仍在應用程式目錄中，請使用 dotnet add package 命令安裝適用於 .NET Core 的 Azure Cosmos DB 用戶端程式庫。

   ```bash
   dotnet add package Microsoft.Azure.Cosmos
   ```

## <a name="step-4-get-your-azure-cosmos-account-credentials"></a>步驟 4：取得 Azure Cosmos 帳戶認證

範例應用程式需要驗證您的 Azure Cosmos 帳戶。 若要進行驗證，您應該將 Azure Cosmos 帳號認證傳遞給應用程式。 請依照下列步驟取得您的 Azure Cosmos 帳戶認證：

1.  登入 [Azure 入口網站](https://portal.azure.com/)。
1.  瀏覽至 Azure Cosmos 帳戶。
1.  開啟 [金鑰]  窗格，然後複製您帳戶的 **URI** 與**主索引鍵**。

如果您使用的是 Azure Cosmos DB 模擬器，請[從此發行項中取得模擬器認證](local-emulator.md#authenticate-requests)。

## <a name="step-5-initialize-the-cosmosclient-object-with-bulk-execution-support"></a>步驟 5：初始化支援大量執行的 CosmosClient 物件

在程式碼編輯器中開啟產生的 `Program.cs` 檔案。 您將為已啟用大量執行的 CosmosClient 建立新的執行個體，並用於對 Azure Cosmos DB 執行作業。 

讓我們從覆寫預設的 `Main` 方法並定義全域變數開始。 這些全域變數將包含端點和授權金鑰、資料庫名稱、您將建立的容器，以及您將大量插入的項目數目。 請務必根據您的環境取代 endpointURL 和授權金鑰值。 


   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Diagnostics;
   using System.IO;
   using System.Text.Json;
   using System.Threading.Tasks;
   using Microsoft.Azure.Cosmos;

   public class Program
   {
        private const string EndpointUrl = "https://<your-account>.documents.azure.com:443/";
        private const string AuthorizationKey = "<your-account-key>";
        private const string DatabaseName = "bulk-tutorial";
        private const string ContainerName = "items";
        private const int ItemsToInsert = 300000;

        static async Task Main(string[] args)
        {

        }
   }
   ```

在 `Main` 方法中，新增下列程式碼以初始化 CosmosClient 物件：

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=CreateClient)]

啟用大量執行之後，CosmosClient 會在內部將並行作業組為單一服務呼叫。 如此一來，它會藉由在分割區之間發佈服務呼叫，最後再將個別結果指派給原始呼叫端的方式，以將輸送量使用率最佳化。

接著，您可以建立容器來儲存我們所有的項目。  將 `/pk` 定義為分割區索引鍵，定義每秒 50000 RU 為佈建的輸送量，並定義自訂編製索引原則來排除所有欄位，以將寫入輸送量最佳化。 在 CosmosClient 初始化陳述式後面新增下列程式碼：

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Initialize)]

## <a name="step-6-populate-a-list-of-concurrent-tasks"></a>步驟 6：填入並行工作清單

若要利用大量執行支援，請根據資料來源和您想要執行的作業來建立非同步工作清單，並使用 `Task.WhenAll` 來同時執行它們。
讓我們從使用「假」資料開始，從我們的資料模型產生項目清單。 在真實世界的應用程式中，項目是來自您所需的資料來源。

首先，使用 dotnet add package 命令，將假封裝新增至解決方案。

   ```bash
   dotnet add package Bogus
   ```

定義所要儲存項目的定義。 您必須在 `Program.cs` 檔案內定義 `Item` 類別：

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Model)]

接下來，在 `Program` 類別內建立 helper 函數。 此 helper 函數會取得您定義要插入的項目數目，並產生隨機資料：

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Bogus)]

讀取項目，並使用 `System.Text.Json` 類別將項目序列化至資料流執行個體。 由於自動產生資料的性質，資料會序列化為資料流。 您也可以直接使用項目執行個體，但透過將它們轉換成資料流，您便可以在 CosmosClient 中利用資料流 API 的效能。 通常只要您知道分割區索引鍵，就可以直接使用資料。 


若要將資料轉換成資料流執行個體，請在 `Main` 方法中，於建立容器之後隨即加入下列程式碼：

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Operations)]

接下來，使用資料流來建立並行工作，並填入工作清單以將項目插入容器中。 若要執行這項操作，請將下列程式碼新增至 `Program` 類別：

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=ConcurrentTasks)]

這些並行點作業會全部一起執行 (亦即大量)，如簡介一節中所述。

## <a name="step-7-run-the-sample"></a>步驟 7：執行範例

若要執行範例，只要透過 `dotnet` 命令來執行此動作：

   ```bash
   dotnet run
   ```

## <a name="get-the-complete-sample"></a>取得完整範例

如果您沒有時間完成本教學課程中的步驟，或只想要下載程式碼範例，您可以從 [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-bulk-import-throughput-optimizer) 取得程式碼。

複製專案之後，請務必在 [Program.cs](https://github.com/Azure-Samples/cosmos-dotnet-bulk-import-throughput-optimizer/blob/master/src/Program.cs#L25) 內更新所需的認證。

您可以透過變更為存放庫目錄並使用 `dotnet`，來執行此範例：

   ```bash
   cd cosmos-dotnet-bulk-import-throughput-optimizer
   dotnet run
   ```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已完成下列步驟：

> [!div class="checklist"]
> * 建立 Azure Cosmos 帳戶
> * 設定您的專案
> * 連線至已啟用大量支援的 Azure Cosmos 帳戶
> * 透過並行建立作業執行資料匯入

現在您可以繼續進行下一個教學課程：

> [!div class="nextstepaction"]
>[使用 SQL API 查詢 Azure Cosmos DB](tutorial-query-sql-api.md)
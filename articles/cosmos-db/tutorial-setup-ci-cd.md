---
title: 使用 Azure Cosmos DB 模擬器建置工作來設定 CI/CD 管線
description: 如何使用 Cosmos DB 模擬器建置工作，在 Azure DevOps 中設定組建和發行工作流程的教學課程
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 01/28/2020
ms.author: dech
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: af3c8713b70911399b2382184dc9fd78d585e03a
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91540280"
---
# <a name="set-up-a-cicd-pipeline-with-the-azure-cosmos-db-emulator-build-task-in-azure-devops"></a>使用 Azure Cosmos DB 模擬器建置工作在 Azure DevOps 中設定 CI/CD 管線

Azure Cosmos DB 模擬器提供了一個模擬 Azure Cosmos DB 服務的本機環境，以供開發之用。 模擬器可讓您在本機開發及測試應用程式，不需建立 Azure 訂用帳戶，也不會產生任何費用。 

適用於 Azure DevOps 的 Azure Cosmos DB 模擬器建置工作，可讓您執行與在 CI 環境中相同的工作。 您可以使用建置工作，在建置和發行工作流程當中，針對模擬器執行測試。 工作會啟動 Docker 容器，且模擬器已在執行中，並提供可由組建定義其餘部分使用的端點。 您可以視需要建立及啟動任意數量的模擬器執行個體，每個執行個體會在個別的容器中執行。 

本文示範如何在 Azure DevOps 中，針對使用 Cosmos DB 模擬器建置工作執行測試的 ASP.NET 應用程式，設定 CI 管線。 您可以使用類似的方法為 Node.js 或 Python 應用程式設定 CI 管線。 

## <a name="install-the-emulator-build-task"></a>安裝模擬器建置工作

若要使用建置工作，首先我們需要將它安裝到 Azure DevOps 組織。 在 [Marketplace](https://marketplace.visualstudio.com/items?itemName=azure-cosmosdb.emulator-public-preview) (英文) 中尋找 **Azure Cosmos DB 模擬器**擴充功能，然後按一下 [免費取得]。

:::image type="content" source="./media/tutorial-setup-ci-cd/addExtension_1.png" alt-text="在 Azure DevOps Marketplace 中尋找 Azure Cosmos DB 模擬器建置工作並進行安裝":::

接下來，選擇要在其中安裝擴充功能的組織。 

> [!NOTE]
> 若要將擴充功能安裝到 Azure DevOps 組織，您必須是帳戶擁有者或專案集合管理員。 如果您沒有權限，但是您是帳戶成員，可以改為要求擴充功能。 [深入了解。](https://docs.microsoft.com/azure/devops/marketplace/faq-extensions?view=vsts)

:::image type="content" source="./media/tutorial-setup-ci-cd/addExtension_2.png" alt-text="在 Azure DevOps Marketplace 中尋找 Azure Cosmos DB 模擬器建置工作並進行安裝":::

## <a name="create-a-build-definition"></a>建立組建定義

在安裝擴充功能後，現在請登入您的 Azure DevOps 組織，並從專案儀表板中尋找您的專案。 您可以將[組建管線](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav)新增至您的專案，或修改現有的組建管線。 如果您已有組建定義，可以直接跳到[將模擬器建置工作新增至組建定義](#addEmulatorBuildTaskToBuildDefinition)。

1. 若要建立新的組建定義，請瀏覽至 Azure DevOps 中的 [**組建**] 索引標籤。 選取 [ **+新建**]。 \> [新增組建管線]

   :::image type="content" source="./media/tutorial-setup-ci-cd/CreateNewBuildDef_1.png" alt-text="在 Azure DevOps Marketplace 中尋找 Azure Cosmos DB 模擬器建置工作並進行安裝":::

2. 依序選取所需的**來源**、[Team 專案]、[存放庫]、[手動和排程組建的預設分支]。 選擇所需的選項後，請選取 [繼續]

   :::image type="content" source="./media/tutorial-setup-ci-cd/CreateNewBuildDef_2.png" alt-text="在 Azure DevOps Marketplace 中尋找 Azure Cosmos DB 模擬器建置工作並進行安裝":::

3. 最後，選取所需的組建管線範本。 我們會在本教學課程中選取 **ASP.NET** 範本。 現在，您已有可設定成使用 Azure Cosmos DB 模擬器建置工作的組建管線。 

> [!NOTE]
> 要選取用於此 CI 的代理程式集區應該已安裝適用於 Windows 的 Docker，除非在先前工作中手動安裝成為 CI 的一部分。 請參閱 [Microsoft 託管代理程式](https://docs.microsoft.com/azure/devops/pipelines/agents/hosted?view=azure-devops&tabs=yaml) (英文) 一文中精選的代理程式集區；建議從 `Hosted VS2017` 著手。

Azure Cosmos DB 模擬器目前不支援託管的 VS2019 代理程式集區。 不過，模擬器已經安裝 VS2019，您可以使用下列 PowerShell Cmdlet 來啟動模擬器，加以使用。 如果您在使用 VS2019 時遇到任何問題，請連絡 [Azure DevOps](https://developercommunity.visualstudio.com/spaces/21/index.html) 小組尋求協助：

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
Start-CosmosDbEmulator
```

## <a name="add-the-task-to-a-build-pipeline"></a><a name="addEmulatorBuildTaskToBuildDefinition"></a>將工作新增至組建管線

1. 將工作新增至組建管線之前，您應先新增代理程式作業。 請瀏覽至您的組建管線並選取 **...** ，然後選擇 [新增代理程式作業]。

1. 接著，選取代理程式作業旁的 **+** 符號，以新增模擬器組建工作。 在搜尋方塊中搜尋 **cosmos**，選取 [Azure Cosmos DB 模擬器]，並將其新增至代理程式作業。 建置工作會啟動已有 Cosmos DB 模擬器執行個體執行於其上的容器。 Azure Cosmos DB 模擬器工作應安排在預期模擬器處於執行中狀態的任何其他工作之前。

   :::image type="content" source="./media/tutorial-setup-ci-cd/addExtension_3.png" alt-text="在 Azure DevOps Marketplace 中尋找 Azure Cosmos DB 模擬器建置工作並進行安裝":::

在本教學課程中，您會先新增工作，以在執行測試之前確定有可用的模擬器。

### <a name="add-the-task-using-yaml"></a>使用 YAML 新增工作

這是選擇性步驟，只有在您使用 YAML 工作來設定 CI/CD 管線時才需要進行。 在這類情況下，您可以定義 YAML 工作，如下列程式碼所示：

```yml
- task: azure-cosmosdb.emulator-public-preview.run-cosmosdbemulatorcontainer.CosmosDbEmulator@2
  displayName: 'Run Azure Cosmos DB Emulator'

- script: yarn test
  displayName: 'Run API tests (Cosmos DB)'
  env:
    HOST: $(CosmosDbEmulator.Endpoint)
    # Hardcoded key for emulator, not a secret
    AUTH_KEY: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
    # The emulator uses a self-signed cert, disable TLS auth errors
    NODE_TLS_REJECT_UNAUTHORIZED: '0'
```

## <a name="configure-tests-to-use-the-emulator"></a>設定測試使用模擬器

現在，我們會設定我們的測試使用模擬器。 模擬器建置工作會匯出環境變數 (CosmosDbEmulator.Endpoint)，建置管線中任何進一步的工作都可以針對該變數發出要求。 

在本教學課程中，我們會使用 [Visual Studio 測試工作](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/VsTestV2/README.md) (英文) 來執行單元測試 (該測試會透過 **.runsettings** 檔案來設定)。 若要深入了解單位測試設定，請造訪[文件](https://docs.microsoft.com/visualstudio/test/configure-unit-tests-by-using-a-dot-runsettings-file?view=vs-2017)。 在 [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-todo-app) 上可取得您在本文件中使用的完整 Todo 應用程式的程式碼範例

以下是 **.runsettings** 檔案的範例，該檔案會定義要傳遞至應用程式單元測試的參數。 請注意，所使用的 `authKey` 變數是模擬器的[已知金鑰](https://docs.microsoft.com/azure/cosmos-db/local-emulator#authenticating-requests)。 這個 `authKey` 是模擬器建置工作所預期的金鑰，應該在您的 **.runsettings** 檔案中定義。

```csharp
<RunSettings>
    <TestRunParameters>
    <Parameter name="endpoint" value="https://localhost:8081" />
    <Parameter name="authKey" value="C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==" />
    <Parameter name="database" value="ToDoListTest" />
    <Parameter name="collection" value="ItemsTest" />
  </TestRunParameters>
</RunSettings>
```

如果您要為使用 Azure Cosmos DB MongoDB API 的應用程式設定 CI/CD 管線，MongoDB 連接字串會預設包含連接埠號碼 10255。 不過，此連接埠目前未開放，您應改為使用連接埠 10250 來建立連線。 除非支援的連接埠號碼是 10250 而不是 10255，否則 Azure Cosmos DB MongoDB API 連接字串會維持不變。

這些參數 `TestRunParameters` 會透過應用程式測試專案中的 `TestContext` 屬性進行參考。 以下是針對 Cosmos DB 執行測試的範例。

```csharp
namespace todo.Tests
{
    [TestClass]
    public class TodoUnitTests
    {
        public TestContext TestContext { get; set; }

        [TestInitialize()]
        public void Initialize()
        {
            string endpoint = TestContext.Properties["endpoint"].ToString();
            string authKey = TestContext.Properties["authKey"].ToString();
            Console.WriteLine("Using endpoint: ", endpoint);
            DocumentDBRepository<Item>.Initialize(endpoint, authKey);
        }
        [TestMethod]
        public async Task TestCreateItemsAsync()
        {
            var item = new Item
            {
                Id = "1",
                Name = "testName",
                Description = "testDescription",
                Completed = false,
                Category = "testCategory"
            };

            // Create the item
            await DocumentDBRepository<Item>.CreateItemAsync(item);
            // Query for the item
            var returnedItem = await DocumentDBRepository<Item>.GetItemAsync(item.Id, item.Category);
            // Verify the item returned is correct.
            Assert.AreEqual(item.Id, returnedItem.Id);
            Assert.AreEqual(item.Category, returnedItem.Category);
        }

        [TestCleanup()]
        public void Cleanup()
        {
            DocumentDBRepository<Item>.Teardown();
        }
    }
}
```

巡覽至 Visual Studio 測試工作中的執行選項。 在 [設定檔] 選項中，請指定測試將使用 **.runsettings** 檔案進行設定。 在 [覆寫測試回合參數] 選項中，於 `-endpoint $(CosmosDbEmulator.Endpoint)` 中新增。 如此一來，就會設定測試工作參考模擬器建置工作的端點，而不是在 **.runsettings** 檔案中定義的端點。  

:::image type="content" source="./media/tutorial-setup-ci-cd/addExtension_5.png" alt-text="在 Azure DevOps Marketplace 中尋找 Azure Cosmos DB 模擬器建置工作並進行安裝":::

## <a name="run-the-build"></a>執行組建

現在，請將組建**儲存並排入佇列中**。 

:::image type="content" source="./media/tutorial-setup-ci-cd/runBuild_1.png" alt-text="在 Azure DevOps Marketplace 中尋找 Azure Cosmos DB 模擬器建置工作並進行安裝":::

一旦開始建置，請觀察 Cosmos DB 模擬器工作是否開始使用安裝的模擬器下拉 Docker 映像。 

:::image type="content" source="./media/tutorial-setup-ci-cd/runBuild_4.png" alt-text="在 Azure DevOps Marketplace 中尋找 Azure Cosmos DB 模擬器建置工作並進行安裝":::

在建置完成之後，請觀察您的測試是否通過，所有測試都是針對來自建置工作的 Cosmos DB 模擬器而執行！

:::image type="content" source="./media/tutorial-setup-ci-cd/buildComplete_1.png" alt-text="在 Azure DevOps Marketplace 中尋找 Azure Cosmos DB 模擬器建置工作並進行安裝":::

## <a name="next-steps"></a>後續步驟

若要深入了解使用模擬器來進行本機開發和測試，請參閱[使用 Azure Cosmos DB 模擬器進行本機開發和測試](https://docs.microsoft.com/azure/cosmos-db/local-emulator)。

若要匯出模擬器 TLS/SSL 憑證，請參閱[匯出 Azure Cosmos DB 模擬器憑證，以便與 Java、Python 和 Node.js 搭配使用](https://docs.microsoft.com/azure/cosmos-db/local-emulator-export-ssl-certificates)

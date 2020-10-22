---
title: 將應用程式套件部署到計算節點
description: 使用 Azure Batch 的應用程式封裝功能輕鬆地管理多個應用程式和版本，以便安裝在 Batch 計算節點。
ms.topic: how-to
ms.date: 09/24/2020
ms.custom:
- H1Hack27Feb2017
- devx-track-csharp
- contperfq1
ms.openlocfilehash: 1bacb0c71c05aeb983bfa9ebf71873a22fea39a1
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "91277694"
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>使用 Batch 應用程式套件將應用程式部署至計算節點

應用程式封裝可以簡化 Azure Batch 解決方案中的程式碼，並讓您更輕鬆地管理工作執行的應用程式。 使用應用程式封裝，您可以上傳及管理工作執行的多個應用程式版本，包括其支援檔案。 接著，您可以將一或多個這種類型的應用程式自動部署到集區中的計算節點。

用來建立和管理應用程式套件的 API 屬於 [Batch Management .NET](/dotnet/api/overview/azure/batch/management) 程式庫的一部分。 用來在計算節點上安裝應用程式套件的 API 則屬於 [Batch .NET](/dotnet/api/overview/azure/batch/client) 程式庫的一部分。 其他語言的類似功能在其可用 Batch API 中。

本文說明如何上傳及管理 Azure 入口網站中的應用程式套件。 它也會示範如何使用 [Batch .net](/dotnet/api/overview/azure/batch/client) 程式庫將它們安裝在集區的計算節點上。

## <a name="application-package-requirements"></a>應用程式封裝需求

若要使用應用程式套件，您必須[將 Azure 儲存體帳戶連結](#link-a-storage-account)到 Batch 帳戶。

Batch 帳戶中的應用程式和應用程式套件數目，以及應用程式套件的大小上限有其限制。 如需詳細資訊，請參閱 [Azure Batch 服務的配額和限制](batch-quota-limit.md)。

> [!NOTE]
> 2017年7月5日之前建立的 Batch 集區不支援應用程式套件 (除非使用雲端服務設定) 在2016年3月10日之後建立它們。 此處所述的應用程式套件功能取代了舊版服務中的「Batch Apps」功能。

## <a name="understand-applications-and-application-packages"></a>瞭解應用程式和應用程式封裝

在 Azure Batch 中，「應用程式」  是指一組已建立版本的二進位檔，這些檔案可自動下載到集區中的計算節點。 應用程式包含一個或多個 *應用程式封裝*，代表應用程式的不同版本。

每個 *應用程式封裝* 都是一個 .zip 檔案，其中包含應用程式二進位檔和任何支援的檔案。 只支援 .zip 格式。

:::image type="content" source="media/batch-application-packages/app_pkg_01.png" alt-text="此圖顯示應用程式和應用程式封裝的高階觀點。":::

您可以在集區或工作層級指定應用程式封裝。

- **集區應用程式套件**會部署到集區中的「每個」節點。 當節點加入集區以及重新啟動或重新安裝映像時，就會部署應用程式。
  
    當集區中的所有節點都執行作業的工作時，集區應用程式封裝是適當的。 當您建立集區時，可以指定一或多個要部署的應用程式封裝。 您也可以新增或更新現有集區的封裝。 若要將新的封裝安裝到現有的集區，您必須重新開機其節點。

- **工作應用程式套件** 只會部署到排程要執行工作的計算節點。 如果節點上已有指定的應用程式套件和版本，則不會重新部署，而會使用現有套件。
  
    工作應用程式封裝適用于共用集區環境，其中不同的作業會在一個集區上執行，而且當作業完成時，不會刪除集區。 如果您的作業所擁有的工作少於集區中的節點，則工作應用程式封裝可以將資料傳輸降到最低，因為您的應用程式只會部署至執行工作的節點。
  
    其他可受益於工作應用程式套件的情節為執行大型應用程式，但只用於少數工作的作業。 例如，工作應用程式可能適用于重型前置處理階段或合併工作。

使用應用程式套件，集區的啟動工作就不需要指定在節點上安裝一長串的個別資源檔案。 您不需要在 Azure 儲存體中或在節點上手動管理應用程式檔案的多個版本。 此外，您不需要擔心產生 [SAS url](../storage/common/storage-sas-overview.md) 來提供儲存體帳戶中檔案的存取權。 Batch 會在背景中與 Azure 儲存體合作來儲存應用程式套件，並將其部署到計算節點。

> [!NOTE]
> 啟動工作的總大小必須小於或等於 32768 個字元，包括資源檔案和環境變數。 如果您的啟動工作超過此限制，則使用應用程式封裝是另一個選項。 您也可以建立包含資源檔的 .zip 檔案、將它以 blob 形式上傳至 Azure 儲存體，然後從啟動工作的命令列將它解壓縮。

## <a name="upload-and-manage-applications"></a>上傳及管理應用程式

您可以使用 [Azure 入口網站](https://portal.azure.com)或 Batch Management API 來管理 Batch 帳戶中的應用程式套件。 下列各節說明如何連結儲存體帳戶，以及如何在 Azure 入口網站中新增和管理應用程式和應用程式封裝。

### <a name="link-a-storage-account"></a>連結儲存體帳戶

若要使用應用程式封裝，您必須將 [Azure 儲存體帳戶](accounts.md#azure-storage-accounts) 連結至您的 Batch 帳戶。 Batch 服務會使用相關聯的儲存體帳戶來儲存您的應用程式封裝。 建議您建立專供 Batch 帳戶使用的儲存體帳戶。

如果您尚未設定儲存體帳戶，Azure 入口網站在您第一次在 Batch 帳戶中選取 **應用程式** 時，會顯示警告。 若要將儲存體帳戶連結到您的 Batch 帳戶，請選取 [**警告**] 視窗上的 [**儲存體帳戶**]，然後再次選取 [**儲存體帳戶**]。

在連結兩個帳戶之後，Batch 便能將儲存在連結之儲存體帳戶中的封裝自動部署到計算節點。

> [!IMPORTANT]
> 您無法將應用程式套件與使用 [防火牆規則](../storage/common/storage-network-security.md)設定的 Azure 儲存體帳戶搭配使用，或在 **階層命名空間** 設定為 **啟用**的情況下使用。

Batch 服務會使用 Azure 儲存體將應用程式套件儲存為區塊 Blob。 針對區塊 Blob 資料，您需[支付標準費用](https://azure.microsoft.com/pricing/details/storage/)，而每個套件的大小則不能超過區塊 Blob 大小上限。 如需詳細資訊，請參閱[儲存體帳戶的 Azure 儲存體延展性和效能目標](../storage/blobs/scalability-targets.md)。 為了將成本降至最低，請務必考慮應用程式封裝的大小和數目，並定期移除已淘汰的封裝。

### <a name="view-current-applications"></a>檢視目前的應用程式

若要查看 Batch 帳戶中的應用程式，請在左側導覽功能表中選取 [ **應用程式** ]。

:::image type="content" source="media/batch-application-packages/app_pkg_02.png" alt-text="此圖顯示應用程式和應用程式封裝的高階觀點。":::

選取此功能表選項會開啟 [ **應用程式** ] 視窗。 此視窗會顯示帳戶中每個應用程式的識別碼，以及下列屬性︰

- **套件**：與此應用程式相關聯的版本號碼。
- **預設版本**：適用時，如果部署應用程式時未指定任何版本，將會安裝的應用程式版本。
- **允許更新**：指定是否允許套件更新和刪除。

若要查看計算節點上應用程式封裝的檔案 [結構](files-and-directories.md) ，請在 Azure 入口網站中流覽至您的 Batch 帳戶。 選取 **集**區。 然後選取包含計算節點的集區。 選取應用程式封裝安裝所在的計算節點，然後開啟 [ **應用程式** ] 資料夾。

### <a name="view-application-details"></a>檢視應用程式詳細資料

若要查看應用程式的詳細資料，請在 [ **應用程式** ] 視窗中選取它。 您可以設定應用程式的下列設定。

- **允許更新**：指出是否可以 [更新或刪除](#update-or-delete-an-application-package)應用程式封裝。 預設值為 [是]。 如果設定為 [ **否**]，則無法更新或刪除現有的應用程式封裝，但仍可加入新的應用程式套件版本。
- **預設版本**：部署應用程式時要使用的預設應用程式封裝（如果未指定任何版本）。
- **顯示名稱**：您的 Batch 解決方案在顯示應用程式相關資訊時可使用的易記名稱。 例如，此名稱可用於您透過 Batch 提供給客戶之服務的 UI 中。

### <a name="add-a-new-application"></a>加入新的應用程式

若要建立新的應用程式，您可以新增應用程式封裝，並指定唯一的應用程式識別碼。

在 Batch 帳戶中，選取 [ **應用程式** ]，然後選取 [ **新增**]。

:::image type="content" source="media/batch-application-packages/app_pkg_05.png" alt-text="此圖顯示應用程式和應用程式封裝的高階觀點。":::

輸入以下資訊：

- **應用程式識別碼**：新應用程式的識別碼。
- **版本**"：您正在上傳之應用程式封裝的版本。
- **應用程式套件**：包含執行應用程式所需之應用程式二進位檔和支援檔案的 .zip 檔案。

您輸入的 **應用程式識別碼** 和 **版本** 必須遵循下列需求：

- 在 Windows 節點上，識別碼可包含英數字元、連字號及底線的任意組合。 在 Linux 節點上，只允許使用英數字元和底線。
- 不可包含超過64個字元。
- 在 Batch 帳戶內必須是唯一的。
- 識別碼會保留大小寫且不區分大小寫。

當您準備好時，請選取 [提交]。 將 .zip 檔案上傳到您的 Azure 儲存體帳戶之後，入口網站會顯示通知。 視您上傳的檔案大小和網路連線的速度而定，這可能需要一些時間。

### <a name="add-a-new-application-package"></a>加入新應用程式封裝

若要為現有的應用程式新增應用程式套件版本，請在 Batch 帳戶的 [ **應用程式** ] 區段中選取應用程式，然後選取 [ **新增**]。

如同您針對新的應用程式所做的一樣，請指定新套件的 **版本** ，並在 [ **應用程式封裝** ] 欄位中上傳您的 .zip 檔案，然後選取 [ **提交**]。

### <a name="update-or-delete-an-application-package"></a>更新或刪除應用程式封裝

若要更新或刪除現有的應用程式封裝，請在 Batch 帳戶的 [ **應用程式** ] 區段中選取應用程式。 選取您要修改之應用程式封裝的資料列中的省略號，然後選取您要執行的動作。

:::image type="content" source="media/batch-application-packages/app_pkg_07.png" alt-text="此圖顯示應用程式和應用程式封裝的高階觀點。":::

如果您選取 [ **更新**]，就可以上傳新的 .zip 檔案。 這會取代您針對該版本上傳的先前 .zip 檔案。

如果您選取 [ **刪除**]，系統會提示您確認刪除該版本。 當您選取 **[確定]** 之後，Batch 將會從您的 Azure 儲存體帳戶中刪除 .zip 檔。 如果您刪除應用程式的預設版本，則會移除該應用程式的 **預設版本** 設定。

## <a name="install-applications-on-compute-nodes"></a>將應用程式安裝在計算節點上

現在您已瞭解如何管理 Azure 入口網站中的應用程式套件，接下來我們可以討論如何將它們部署到計算節點，並使用 Batch 工作來執行它們。

### <a name="install-pool-application-packages"></a>安裝集區應用程式套件

若要將應用程式套件安裝在集區中的所有計算節點上，請為集區指定一或多個應用程式套件「參考」  。 您為集區指定的應用程式封裝會安裝在每個加入集區的計算節點上，以及重新開機或重新安裝映射的任何節點上。

在 Batch .NET 中，請在建立新集區或針對現有集區時，指定一或多個 [>cloudpool.maxtaskspercomputenode. >applicationpackagereferences](/dotnet/api/microsoft.azure.batch.cloudpool.applicationpackagereferences) 。 [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference) 類別能指定要安裝在集區之計算節點上的應用程式識別碼和版本。

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicatedComputeNodes: 1,
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "1.1001.2b" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package is installed on each.
await myCloudPool.CommitAsync();
```

> [!IMPORTANT]
> 如果應用程式套件部署失敗，批次服務會將節點標示為 [無法使用](/dotnet/api/microsoft.azure.batch.computenode.state)，而且不會在該節點上排程執行任何工作。 如果發生這種情況，請重新開機節點以重新起始封裝部署。 重新啟動節點也會在節點上再次啟用工作排程。

### <a name="install-task-application-packages"></a>安裝工作應用程式套件

類似於集區，您可以為工作指定應用程式套件「參考」  。 在節點上排程要執行的工作時，會先下載並解壓縮套件，再執行工作的命令列。 如果節點上已安裝指定的套件和版本，則不會下載套件，而會使用現有套件裝。

若要安裝工作應用程式套件，請設定工作的 [CloudTask >applicationpackagereferences](/dotnet/api/microsoft.azure.batch.cloudtask.applicationpackagereferences) 屬性：

```csharp
CloudTask task =
    new CloudTask(
        "litwaretask001",
        "cmd /c %AZ_BATCH_APP_PACKAGE_LITWARE%\\litware.exe -args -here");

task.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference
    {
        ApplicationId = "litware",
        Version = "1.1001.2b"
    }
};
```

## <a name="execute-the-installed-applications"></a>執行安裝的應用程式

您為集區或工作所指定的套件會下載並解壓縮至節點的 `AZ_BATCH_ROOT_DIR` 中的具名目錄。 Batch 也會建立包含具名目錄路徑的環境變數。 在參考節點上的應用程式時，工作的命令列會使用這個環境變數。

在 Windows 節點上，變數格式如下：

```
Windows:
AZ_BATCH_APP_PACKAGE_APPLICATIONID#version
```

在 Linux 節點上，格式稍有不同。 句號 (.)、連字號 (-) 和數字記號 (#) 已壓平合併為環境變數中的底線。 另外請注意，會保留應用程式識別碼的大小寫。 例如：

```
Linux:
AZ_BATCH_APP_PACKAGE_applicationid_version
```

`APPLICATIONID` 和 `version` 是對應於為部署所指定的應用程式和套件版本的值。 例如，如果您指定應該在 Windows 節點上安裝 2.7 版的「Blender」應用程式，您的工作命令列就會使用此環境變數來存取其檔案︰

```
Windows:
AZ_BATCH_APP_PACKAGE_BLENDER#2.7
```

在 Linux 節點上，以此格式指定環境變數。 將句號 (.)、連字號 (-) 和數字符號 (#) 壓平合併化為底線，並保留應用程式識別碼的大小寫：

```
Linux:
AZ_BATCH_APP_PACKAGE_blender_2_7
```

當您上傳應用程式套件時，您可以指定要部署至運算節點的預設版本。 如果您已經指定應用程式的預設版本，當您參考應用程式時就可以省略版本尾碼。 您可以在 Azure 入口網站中的 [應用程式] 視窗上指定預設應用程式版本，如[上傳及管理應用程式](#upload-and-manage-applications)中所示。

例如，如果您設定「2.7」作為「Blender」應用程式的預設版本，且您的工作是參考下列環境變數，您的 Windows 節點就會執行 2.7 版：

`AZ_BATCH_APP_PACKAGE_BLENDER`

下列程式碼片段會顯示工作命令列範例，其可啟動「Blender」  應用程式的預設版本︰

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> 如需計算節點環境設定的詳細資訊，請參閱[工作的環境設定](jobs-and-tasks.md#environment-settings-for-tasks)。

## <a name="update-a-pools-application-packages"></a>更新集區的應用程式封裝

如果您已設定現有集區的應用程式封裝，可以指定集區的新封裝。 這表示：

- Batch 服務會在加入新增集區的所有新節點，以及任何重新啟動或重新安裝映像的現有節點上安裝新指定的套件。
- 在更新封裝參考時已存在集區中的計算節點不會自動安裝新應用程式封裝。 這些計算節點必須重新啟動或重新安裝映像才能接收新封裝。
- 部署新的封裝之後，所建立的環境變數會反映新的應用程式封裝參考。

在此範例中，現有集區已將 *blender* 應用程式的2.7 版設定為其中一個 [>cloudpool.maxtaskspercomputenode. >applicationpackagereferences](/dotnet/api/microsoft.azure.batch.cloudpool.applicationpackagereferences)。 若要將集區的節點更新為 2.76b 版，請將 [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference) 指定為新版本並認可變更。

```csharp
string newVersion = "2.76b";
CloudPool boundPool = await batchClient.PoolOperations.GetPoolAsync("myPool");
boundPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "blender",
        Version = newVersion }
};
await boundPool.CommitAsync();
```

既然您已設定新版本，Batch 服務就會將 2.76b 版安裝到任何加入集區的「新」節點。 若要將 2.76b 安裝在「已存在」  集區中的節點上，請將節點重新啟動或重新安裝映像。 請注意，重新開機的節點會保留先前套件部署的檔案。

## <a name="list-the-applications-in-a-batch-account"></a>列出 Batch 帳戶中的應用程式

您可以使用 [>applicationoperations >listapplicationsummaries](/dotnet/api/microsoft.azure.batch.applicationoperations.listapplicationsummaries) 方法，在 Batch 帳戶中列出應用程式及其套件。

```csharp
// List the applications and their application packages in the Batch account.
List<ApplicationSummary> applications = await batchClient.ApplicationOperations.ListApplicationSummaries().ToListAsync();
foreach (ApplicationSummary app in applications)
{
    Console.WriteLine("ID: {0} | Display Name: {1}", app.Id, app.DisplayName);

    foreach (string version in app.Versions)
    {
        Console.WriteLine("  {0}", version);
    }
}
```

## <a name="next-steps"></a>後續步驟

- [Batch REST API](/rest/api/batchservice) 也提供應用程式套件的使用支援。 例如，請參閱 [>applicationpackagereferences](/rest/api/batchservice/pool/add#applicationpackagereference) 元素，以瞭解如何指定要安裝的套件，以及如何取得應用程式資訊的 [應用程式](/rest/api/batchservice/application) 。
- 了解如何以程式設計方式 [使用 Batch Management .NET 管理 Azure Batch 帳戶和配額](batch-management-dotnet.md)。 [Batch Management .NET](/dotnet/api/overview/azure/batch/management) 程式庫可以啟用 Batch 應用程式或服務的帳戶建立和刪除功能。

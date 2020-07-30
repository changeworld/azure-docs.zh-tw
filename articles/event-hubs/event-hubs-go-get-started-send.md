---
title: 快速入門：使用 Go 來傳送和接收事件 - Azure 事件中樞
description: 快速入門：本文將逐步解說如何建立一個會從 Azure 事件中樞傳送事件的 Go 應用程式。
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 59cd0d757108e7579ce389d216b0ee4d569e12fd
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87002449"
---
# <a name="quickstart-send-events-to-or-receive-events-from-event-hubs-using-go"></a>快速入門：使用 Go 將事件傳送至事件中樞或從中接收事件
Azure 事件中樞是巨量資料串流平台和事件擷取服務，每秒可接收和處理數百萬個事件。 事件中樞可以處理及儲存分散式軟體和裝置所產生的事件、資料或遙測。 傳送至事件中樞的資料可以透過任何即時分析提供者或批次/儲存體配接器來轉換和儲存。 如需事件中樞的詳細概觀，請參閱[事件中樞概觀](event-hubs-about.md)和[事件中樞功能](event-hubs-features.md)。

本教學課程說明如何撰寫 Go 應用程式，以將事件傳送至事件中樞或從中接收事件。 

> [!NOTE]
> 您可以從 [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs) 下載此快速入門來作為範例，並以您事件中樞的值取代 `EventHubConnectionString` 和 `EventHubName` 字串，然後執行。 或者，您可以遵循本教學課程中的步驟，來建立自己的解決方案。

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您需要下列必要條件：

- 安裝在本機的 Go。 如果有需要，請依照[這些指示](https://golang.org/doc/install) \(英文\) 執行。
- 使用中的 Azure 帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶][]。
- **建立事件中樞命名空間和事件中樞**。 請使用 [Azure 入口網站](https://portal.azure.com)建立「事件中樞」類型的命名空間，然後取得您的應用程式與「事件中樞」進行通訊所需的管理認證。 若要建立命名空間和事件中樞，請依照[這篇文章](event-hubs-create.md)中的程序操作。

## <a name="send-events"></a>傳送事件
本節說明如何建立可將事件傳送至事件中樞的 Go 應用程式。 

### <a name="install-go-package"></a>安裝 Go 套件

使用 `go get` 或 `dep` 取得適用於事件中樞的 Go 套件。 例如：

```bash
go get -u github.com/Azure/azure-event-hubs-go
go get -u github.com/Azure/azure-amqp-common-go/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
```

### <a name="import-packages-in-your-code-file"></a>在您的程式碼檔案中匯入套件

若要匯入 Go 套件，請使用下列程式碼範例：

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
)
```

### <a name="create-service-principal"></a>建立服務主體

依照[使用Azure CLI 2.0 來建立 Azure 服務主體](/cli/azure/create-an-azure-service-principal-azure-cli)中的指示建立新的服務主體。 使用下列名稱將提供的認證儲存在您的環境中。 Azure SDK for Go 與事件中樞套件預先設定為尋找這些變數名稱：

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

現在，為使用這些認證的事件中樞用戶端建立授權提供者：

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="create-event-hubs-client"></a>建立事件中樞用戶端

下列程式碼會建立事件中樞用戶端：

```go
hub, err := eventhubs.NewHub("namespaceName", "hubName", tokenProvider)
ctx := context.WithTimeout(context.Background(), 10 * time.Second)
defer hub.Close(ctx)
if err != nil {
    log.Fatalf("failed to get hub %s\n", err)
}
```

### <a name="write-code-to-send-messages"></a>撰寫程式碼來傳送訊息

在下列程式碼片段中，使用 (1) 從終端機以互動方式傳送訊息，或使用 (2) 在您的程式內傳送訊息：

```go
// 1. send messages at the terminal
ctx = context.Background()
reader := bufio.NewReader(os.Stdin)
for {
    fmt.Printf("Input a message to send: ")
    text, _ := reader.ReadString('\n')
    hub.Send(ctx, eventhubs.NewEventFromString(text))
}

// 2. send messages within program
ctx = context.Background()
hub.Send(ctx, eventhubs.NewEventFromString("hello Azure!"))
```

### <a name="extras"></a>額外項目

取得事件中樞內的資料分割識別碼：

```go
info, err := hub.GetRuntimeInformation(ctx)
if err != nil {
    log.Fatalf("failed to get runtime info: %s\n", err)
}
log.Printf("got partition IDs: %s\n", info.PartitionIDs)
```

執行應用程式以將事件傳送到事件中樞。 

恭喜！ 您現在已將傳送訊息到事件中樞。

## <a name="receive-events"></a>接收事件

### <a name="create-a-storage-account-and-container"></a>建立儲存體帳戶和容器

分割區上的租用和事件資料流中的檢查點等狀態，都會使用 Azure 儲存體容器在接收者之間共用。 您可以搭配 Go SDK 建立儲存體帳戶和容器，但您也可以依照[關於 Azure 儲存體帳戶](../storage/common/storage-account-create.md)中的指示建立它們。

搭配 Go SDK 建立儲存體構件的範例，可於 [Go 範例存放庫](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage) \(英文\) 和本教學課程所對應的範例中取得。

### <a name="go-packages"></a>Go 套件

若要接收訊息，請透過 `go get` 或 `dep` 取得適用於事件中樞的 Go 套件：

```bash
go get -u github.com/Azure/azure-event-hubs-go/...
go get -u github.com/Azure/azure-amqp-common-go/...
go get -u github.com/Azure/go-autorest/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
dep ensure -add github.com/Azure/go-autorest
```

### <a name="import-packages-in-your-code-file"></a>在您的程式碼檔案中匯入套件

若要匯入 Go 套件，請使用下列程式碼範例：

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
    eph "github.com/Azure/azure-event-hubs-go/eph"
    storageLeaser "github.com/Azure/azure-event-hubs-go/storage"
    azure "github.com/Azure/go-autorest/autorest/azure"
)
```

### <a name="create-service-principal"></a>建立服務主體

依照[使用Azure CLI 2.0 來建立 Azure 服務主體](/cli/azure/create-an-azure-service-principal-azure-cli)中的指示建立新的服務主體。 請使用下列名稱將提供的認證儲存在您的環境中：Azure SDK for Go 與「事件中樞」套件皆已預先設定為會尋找這些變數名稱。

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

接下來，為使用這些認證的事件中樞用戶端建立授權提供者：

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="get-metadata-struct"></a>取得中繼資料結構

使用 Azure Go SDK 取得具有關於您 Azure 環境之中繼資料的結構。 後續作業會使用此結構來尋找正確的端點。

```go
azureEnv, err := azure.EnvironmentFromName("AzurePublicCloud")
if err != nil {
    log.Fatalf("could not get azure.Environment struct: %s\n", err)
}
```

### <a name="create-credential-helper"></a>建立認證協助程式 

建立會使用先前的 Azure Active Directory (AAD) 認證來建立適用於儲存體的共用存取簽章 (SAS) 認證的認證協助程式。 最後一個參數會告訴此建構函式應使用和先前相同的環境變數：

```go
cred, err := storageLeaser.NewAADSASCredential(
    subscriptionID,
    resourceGroupName,
    storageAccountName,
    storageContainerName,
    storageLeaser.AADSASCredentialWithEnvironmentVars())
if err != nil {
    log.Fatalf("could not prepare a storage credential: %s\n", err)
}
```

### <a name="create-a-check-pointer-and-a-leaser"></a>建立 Checkpointer 和 Leaser 

建立負責將分割區租用給特定接收者的 **Leaser**，以及負責為訊息資料流撰寫檢查點，使其他接收者可以從正確的位移開始讀取的 **Checkpointer**。

目前有提供單一的 **StorageLeaserCheckpointer**，能使用相同的儲存體容器來管理租用和檢查點。 除了儲存體帳戶和容器名稱之外，**StorageLeaserCheckpointer** 也需要在上個步驟中所建立的認證及 Azure 環境結構以正確存取容器。

```go
leaserCheckpointer, err := storageLeaser.NewStorageLeaserCheckpointer(
    cred,
    storageAccountName,
    storageContainerName,
    azureEnv)
if err != nil {
    log.Fatalf("could not prepare a storage leaserCheckpointer: %s\n", err)
}
```

### <a name="construct-event-processor-host"></a>建構事件處理器主機

您現在已具備以下列方式建構 EventProcessorHost 所需的所有項目。 如前所述，相同的 **StorageLeaserCheckpointer** 會同時作為 Leaser 和 Checkpointer：

```go
ctx := context.Background()
p, err := eph.New(
    ctx,
    nsName,
    hubName,
    tokenProvider,
    leaserCheckpointer,
    leaserCheckpointer)
if err != nil {
    log.Fatalf("failed to create EPH: %s\n", err)
}
defer p.Close(context.Background())
```

### <a name="create-handler"></a>建立處理常式 

現在請建立處理常式，並向事件處理器主機註冊它。 當主機啟動時，它會將此處理常式及任何其他指定的處理常式套用到傳入的訊息：

```go
handler := func(ctx context.Context, event *eventhubs.Event) error {
    fmt.Printf("received: %s\n", string(event.Data))
    return nil
}

// register the handler with the EPH
_, err := p.RegisterHandler(ctx, handler)
if err != nil {
    log.Fatalf("failed to register handler: %s\n", err)
}
```

### <a name="write-code-to-receive-messages"></a>撰寫程式碼來接收訊息

一切都設定好之後，您便可以搭配 `Start(context)` 啟動事件處理器主機來使它永久執行，或是搭配 `StartNonBlocking(context)` 來僅在有可用訊息的情況下執行。

本教學課程會以下列方式啟動並執行；請參閱 GitHub 範例以取得使用 `StartNonBlocking` 的範例：

```go
ctx := context.Background()
err = p.Start()
if err != nil {
    log.Fatalf("failed to start EPH: %s\n", err)
}
```

## <a name="next-steps"></a>後續步驟
請閱讀下列文章：

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Azure 事件中樞的功能與術語](event-hubs-features.md)
- [事件中樞常見問題集](event-hubs-faq.md)


<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[免費帳戶]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

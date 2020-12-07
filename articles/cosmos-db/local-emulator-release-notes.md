---
title: Azure Cosmos DB 模擬器下載和版本資訊
description: 取得不同版本的 Azure Cosmos DB 模擬器版本資訊和下載資訊。
ms.service: cosmos-db
ms.topic: conceptual
author: milismsft
ms.author: adrianmi
ms.date: 09/21/2020
ms.openlocfilehash: 5d105ad4c2aff03dae52b340037e4c193bf82ca1
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608430"
---
# <a name="azure-cosmos-db-emulator---release-notes-and-download-information"></a>Azure Cosmos DB 模擬器 - 版本資訊和下載資訊
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

本文顯示 Azure Cosmos DB 模擬器版本資訊，以及每個版本中所做的功能更新清單。 它也會列出最新版本的模擬器以供下載和使用。

## <a name="download"></a>下載

| | |
|---------|---------|
|**MSI 下載**|[Microsoft 下載中心](https://aka.ms/cosmosdb-emulator)|
|**開始使用**|[使用 Azure Cosmos 模擬器在本機開發](local-emulator.md)|

## <a name="release-notes"></a>版本資訊

### <a name="2119-3-december-2020"></a>2.11.9 (2020 年 12 月 3 日)

 - 除了反映 Azure Cosmos DB 最新功能和改進的一般內容更新之外，此版本還解決了 Azure Cosmos DB 模擬器功能的幾個問題：
 * 修正了使用直接模式和 JAVA 用戶端應用程式時，大型檔承載要求失敗的問題。
 * 修正了以 .NET 為基礎的應用程式為目標時，MongoDB 端點版本 3.6 的連線問題。

### <a name="2118-6-november-2020"></a>2.11.8 (2020 年 11 月 6 日)

 - 此版本包含 Cosmos 模擬器資料總管的更新，並修正 TLS 1.3 用戶端會嘗試開啟資料總管的問題。

### <a name="2116-6-october-2020"></a>2.11.6 (2020 年 10 月 6 日)

 - 在可能同時建立多個容器時，此版本可解決並行相關問題。 在這類情況下，模擬器的資料會繼續處於損毀狀態，而模擬器端點的下列 API 要求可能會失敗，並出現「服務無法使用」的錯誤，而需要重新啟動並重設模擬器的本機資料。

### <a name="2115-23-august-2020"></a>2.11.5 (2020 年 8 月 23 日)

此版本新增兩個新的 Cosmos 模擬器啟動選項： 

* "/EnablePreview" - 啟用模擬器的預覽功能。 仍在開發中的預覽功能，可透過 CI 和範例撰寫來存取。
* "/EnableAadAuthentication" - 啟用模擬器以接受自訂 Azure Active Directory 權杖作為 Azure Cosmos 主要金鑰的替代方案。 這項功能仍在開發中；目前不支援特定角色指派和其他權限相關設定。

### <a name="2112-07-july-2020"></a>2.11.2 (2020 年 7 月 7 日)

- 此版本會變更如何收集對 Cosmos 模擬器進行疑難排解時所需的 ETL 追蹤。 WPR (Windows 效能執行時間工具) 現在是用於擷取 ETL 型追蹤的預設工具，而舊的 LOGMAN 型擷取則已被取代。 這是必要的變更，部分原因是最新的 Windows 安全性更新在透過 Cosmos 模擬器執行時，會對 LOGMAN 的運作方式產生非預期的影響。

### <a name="2111-10-june-2020"></a>2.11.1 (2020 年 6 月 10 日)

- 此版本修正了與模擬器資料總管相關的幾個錯誤 (bug)。 在某些情況下，若透過網頁瀏覽器使用模擬器資料總管，便無法連線至 Cosmos 模擬器端點，而且所有相關動作 (例如建立資料庫或容器) 都會導致錯誤。 已修正的第二個問題與使用資料總管上傳動作從 JSON 檔案建立項目有關。

### <a name="2110"></a>2.11.0

- 此版本引進自動調整佈建輸送量的支援。 這些新功能包括能夠在要求單位中設定自訂的最大輸送量層級 (RU/秒)、在現有的資料庫和容器上啟用自動調整，以及透過 Azure Cosmos DB SDK 進行程式設計支援。
- 修正在查詢大量文件 (超過 1 GB) 時，模擬器將會失敗，並出現內部錯誤狀態碼 500 的問題。

### <a name="292"></a>2.9.2

- 此版本會修正錯誤 (bug)，同時啟用 MongoDb 端點 3.2 版本的支援。 同時也會使用 WPR (而非 LOGMAN) 來新增為了疑難排解產生 ETL 追蹤的支援。

### <a name="291"></a>2.9.1

- 此版本修正了查詢 API 支援中的幾個問題，並恢復與舊版 OS (例如 Windows Server 2012) 的相容性。

### <a name="290"></a>2.9.0

- 此版本新增了將一致性設定為一致前置詞，以及提高使用者數目上限和權限的選項。

### <a name="272"></a>2.7.2

- 此版本會將 MongoDB 3.6 版伺服器支援新增至 Cosmos 模擬器。 若要啟動以 3.6 版為目標的 MongoDB 端點，請從系統管理員命令列利用 "/EnableMongoDBEndpoint=3.6" 選項啟動模擬器。

### <a name="270"></a>2.7.0

- 此版本修正了以下迴歸：使用者在使用 .NET Core 或 x86 .NET 型用戶端時，無法從模擬器對 SQL API 帳戶執行查詢。

### <a name="246"></a>2.4.6

- 從 2019 年 7 月起，此版本會提供與 Azure Cosmos 服務中功能同等的內容，而[使用 Azure Cosmos 模擬器在本機進行開發](local-emulator.md)中會敘述例外狀況。 其中也會修正下列狀況的數個相關錯誤 (bug)：透過命令列叫用模擬器時造成模擬器關閉，以及 SDK 用戶端使用直接模式連線時，內部 IP 位址遭到覆寫。

### <a name="243"></a>2.4.3

- 停用依預設啟動 MongoDB 服務。 只有 SQL 端點預設為啟用。 使用者必須使用模擬器的 "/EnableMongoDbEndpoint" 命令列選項，以手動啟動端點。 如同所有其他服務端點，如 Gremlin、Cassandra 和資料表。
- 已修正模擬器中的錯誤 (bug)，當從 “/AllowNetworkAccess” 開頭，其中 Gremlin、Cassandra 和資料表端點未正確處理來自外部用戶端的要求。
- 將直接連線連接埠新增到防火牆規則設定中。

### <a name="240"></a>2.4.0

- 已修正當主機電腦上有網路監視應用程式 (如 Pulse Client) 時，模擬器無法啟動的問題。

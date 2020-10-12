---
title: Azure 應用程式組態復原和嚴重損壞修復
description: 瞭解如何使用 Azure 應用程式組態來執行復原和嚴重損壞修復。
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 5c62f10d67345d68cde27af7d0a7663b22d978a0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86207191"
---
# <a name="resiliency-and-disaster-recovery"></a>復原和災害復原

Azure 應用程式組態目前是一項區域性服務。 每個組態存放區都會建立在特定 Azure 區域中。 全區域的服務中斷會影響該區域中的所有存放區。 應用程式組態並未提供自動容錯移轉至另一個區域的功能。 本文提供如何跨 Azure 區域使用多個組態存放區來提升應用程式異地復原能力的一般指引。

## <a name="high-availability-architecture"></a>高可用性架構

若要實現跨區域的備援性，您必須在不同區域建立多個應用程式組態存放區。 透過此設定，您的應用程式至少有一個額外的組態存放區，可在主要存放區無法存取時作為後援存放區。 下圖說明您的應用程式及其主要和次要組態存放區之間的拓撲：

![異地備援存放區](./media/geo-redundant-app-configuration-stores.png)

您的應用程式會以平行方式同時從主要和次要存放區載入其組態。 如此將可提高成功取得組態資料的機率。 您必須負責讓兩個存放區中的資料保持同步。下列各節說明如何在您的應用程式中建立地理復原。

## <a name="failover-between-configuration-stores"></a>組態存放區之間的容錯移轉

技術上，您的應用程式並不會執行容錯移轉。 它會嘗試同時從兩個應用程式組態存放區擷取相同的組態資料集。 編排您的程式碼，使其先從次要存放區載入，然後再從主要存放區載入。 此方法可確保主要存放區中的組態資料在可用時即應優先使用。 下列程式碼片段示範如何在 .NET Core 中執行這種相片順序：

#### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(settings["ConnectionString_SecondaryStore"], optional: true)
                  .AddAzureAppConfiguration(settings["ConnectionString_PrimaryStore"], optional: true);
        })
        .UseStartup<Startup>();
    
```

#### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(settings["ConnectionString_SecondaryStore"], optional: true)
                    .AddAzureAppConfiguration(settings["ConnectionString_PrimaryStore"], optional: true);
            })
            .UseStartup<Startup>());
```
---

請留意傳入 `AddAzureAppConfiguration` 函式中的 `optional` 參數。 此參數設為 `true` 時，會防止應用程式在函式無法載入組態資料時無法繼續執行。

## <a name="synchronization-between-configuration-stores"></a>組態存放區之間的同步處理

您所有的異地備援組態存放區務必要有相同的資料集。 作法有二：

### <a name="backup-manually-using-the-export-function"></a>使用 Export 函數手動備份

您可以使用應用程式組態中的**匯出**函式，隨需將主要存放區中的資料複製到次要存放區。 此函式可透過 Azure 入口網站和 CLI 來執行。

在 Azure 入口網站中，您可以依照下列步驟，將變更推送至另一個組態存放區。

1. 移至 [匯入/匯出]**** 索引標籤，然後選取 [匯出]**** > [應用程式組態]**** > [目標]**** > [選取資源]****。

1. 在開啟的新分頁中，指定次要存放區的訂用帳戶、資源群組和資源名稱， **然後選取 [** 套用]。

1. UI 會更新，供您選擇要匯出至次要存放區的組態資料。 您可以保留預設的時間值，並將其 **從標籤** 和 **標籤** 設定為相同的值。 選取 [套用]。 針對主要存放區中的所有標籤重複此步驟。

1. 每當您的設定變更時，請重複上述步驟。

您也可以使用 Azure CLI 來達成匯出流程。 下列命令顯示如何將所有設定從主要存放區匯出至次要存放區：

```azurecli
    az appconfig kv export --destination appconfig --name {PrimaryStore} --dest-name {SecondaryStore} --label * --preserve-labels -y
```

### <a name="backup-automatically-using-azure-functions"></a>使用 Azure Functions 自動備份

您可以使用 Azure Functions 來自動進行備份程式。 它會在應用程式設定中利用與 Azure 事件方格的整合。 設定完成後，應用程式設定會將事件發佈至事件方格，以針對設定存放區中的索引鍵/值所做的任何變更。 因此，Azure Functions 的應用程式可以接聽這些事件並據以備份資料。 如需詳細資訊，請參閱 [如何自動備份應用程式](./howto-backup-config-store.md)設定存放區的教學課程。

## <a name="next-steps"></a>接下來的步驟

在本文中，您已了解如何擴充應用程式，以達到應用程式組態在執行階段的異地復原能力。 您也可以在建置或部署時內嵌應用程式組態中的組態資料。 如需詳細資訊，請參閱[與 CI/CD 管線整合](./integrate-ci-cd-pipeline.md)。

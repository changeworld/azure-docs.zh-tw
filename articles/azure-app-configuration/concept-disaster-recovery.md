---
title: Azure 應用配置恢復和災害復原
description: 使用 Azure 應用配置實現恢復性和災害復原。
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 96ef09ac081aa328014217592a7fcd3ed6314c0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523759"
---
# <a name="resiliency-and-disaster-recovery"></a>復原和災害復原

Azure 應用程式組態目前是一項區域性服務。 每個組態存放區都會建立在特定 Azure 區域中。 全區域的服務中斷會影響該區域中的所有存放區。 應用程式組態並未提供自動容錯移轉至另一個區域的功能。 本文提供如何跨 Azure 區域使用多個組態存放區來提升應用程式異地復原能力的一般指引。

## <a name="high-availability-architecture"></a>高可用性架構

若要實現跨區域的備援性，您必須在不同區域建立多個應用程式組態存放區。 透過此設定，您的應用程式至少有一個額外的組態存放區，可在主要存放區無法存取時作為後援存放區。 下圖說明您的應用程式及其主要和次要組態存放區之間的拓撲：

![異地備援存放區](./media/geo-redundant-app-configuration-stores.png)

您的應用程式會以平行方式同時從主要和次要存放區載入其組態。 如此將可提高成功取得組態資料的機率。 您有責任使兩個存儲中的資料保持同步。以下各節說明如何將地理恢復性構建到應用程式中。

## <a name="failover-between-configuration-stores"></a>組態存放區之間的容錯移轉

技術上，您的應用程式並不會執行容錯移轉。 它會嘗試同時從兩個應用程式組態存放區擷取相同的組態資料集。 編排您的程式碼，使其先從次要存放區載入，然後再從主要存放區載入。 此方法可確保主要存放區中的組態資料在可用時即應優先使用。 以下程式碼片段顯示了如何在 .NET Core 中實現這種安排：

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

您所有的異地備援組態存放區務必要有相同的資料集。 您可以使用應用程式組態中的**匯出**函式，隨需將主要存放區中的資料複製到次要存放區。 此函式可透過 Azure 入口網站和 CLI 來執行。

在 Azure 入口網站中，您可以依照下列步驟，將變更推送至另一個組態存放區。

1. 移至 [匯入/匯出]**** 索引標籤，然後選取 [匯出]**** > [應用程式組態]**** > [目標]**** > [選取資源]****。

1. 在打開的新邊欄選項卡中，指定輔助存儲的訂閱、資源組和資源名稱，然後選擇 **"應用**"。

1. UI 會更新，供您選擇要匯出至次要存放區的組態資料。 您可以將預設時間值保留為"**從"標籤**和 **"到"標籤**設置為相同的值。 選取 [**套用**]。

1. 重複先前步驟以進行所有組態變更。

若要自動執行此匯出程序，請使用 Azure CLI。 下列命令說明如何將單一組態變更從主要存放區匯出至次要存放區：

```azurecli
    az appconfig kv export --destination appconfig --name {PrimaryStore} --label {Label} --dest-name {SecondaryStore} --dest-label {Label}
```

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何擴充應用程式，以達到應用程式組態在執行階段的異地復原能力。 您也可以在建置或部署時內嵌應用程式組態中的組態資料。 如需詳細資訊，請參閱[與 CI/CD 管線整合](./integrate-ci-cd-pipeline.md)。

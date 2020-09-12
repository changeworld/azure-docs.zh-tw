---
title: 使用多個實例進行調整-Azure SignalR Service
description: 在許多調整案例中，客戶通常需要布建多個實例，並將其設定為搭配使用，以建立大規模部署。 例如，分區化需要多個實例支援。
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 03/27/2019
ms.author: zhshang
ms.openlocfilehash: fd6ac8c4d4fc4c3fec4f549f8ef4f955e2b1c637
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2020
ms.locfileid: "89439209"
---
# <a name="how-to-scale-signalr-service-with-multiple-instances"></a>如何使用多個實例調整 SignalR Service？
最新的 SignalR Service SDK 支援 SignalR Service 實例的多個端點。 您可以使用這項功能來調整並行連接，或將它用於跨區域訊息處理。

## <a name="for-aspnet-core"></a>針對 ASP.NET Core

### <a name="how-to-add-multiple-endpoints-from-config"></a>如何從 config 新增多個端點？

具有索引鍵 `Azure:SignalR:ConnectionString` 或 `Azure:SignalR:ConnectionString:` SignalR Service 連接字串的設定。

如果索引鍵開頭為 `Azure:SignalR:ConnectionString:` ，它應該採用格式 `Azure:SignalR:ConnectionString:{Name}:{EndpointType}` ，其中 `Name` 和 `EndpointType` 是物件的屬性 `ServiceEndpoint` ，而且可從程式碼存取。

您可以使用下列命令來新增多個實例連接字串 `dotnet` ：

```cmd
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-a <ConnectionString1>
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-b:primary <ConnectionString2>
dotnet user-secrets set Azure:SignalR:ConnectionString:backup:secondary <ConnectionString3>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>如何從程式碼新增多個端點？

`ServicEndpoint`引進類別來描述 Azure SignalR Service 端點的屬性。
使用 Azure SignalR Service SDK 時，您可以設定多個實例端點：
```cs
services.AddSignalR()
        .AddAzureSignalR(options => 
        {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString0>"),
                new ServiceEndpoint("<ConnectionString1>", type: EndpointType.Primary, name: "east-region-a"),
                new ServiceEndpoint("<ConnectionString2>", type: EndpointType.Primary, name: "east-region-b"),
                new ServiceEndpoint("<ConnectionString3>", type: EndpointType.Secondary, name: "backup"),
            };
        });
```

### <a name="how-to-customize-endpoint-router"></a>如何自訂端點路由器？

根據預設，SDK 會使用 [DefaultEndpointRouter](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/EndpointRouters/DefaultEndpointRouter.cs) 來挑選端點。

#### <a name="default-behavior"></a>預設行為 
1. 用戶端要求路由

    當用戶端 `/negotiate` 與應用程式伺服器搭配使用時。 SDK 預設會從可用的服務端點集合中 **隨機選取** 一個端點。

2. 伺服器訊息路由

    當 * 傳送訊息至特定的 * * 連接 * * *，而目標連接路由至目前的伺服器時，訊息會直接傳送到該連線的端點。 否則，會將訊息廣播到每個 Azure SignalR 端點。

#### <a name="customize-routing-algorithm"></a>自訂路由演算法
您可以建立自己的路由器，以找出訊息應該前往哪些端點的特殊知識。

自訂路由器的定義如下：當群組開始時， `east-` 一律會移至名為的端點 `east` ：

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override IEnumerable<ServiceEndpoint> GetEndpointsForGroup(string groupName, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the group broadcast behavior, if the group name starts with "east-", only send messages to endpoints inside east
        if (groupName.StartsWith("east-"))
        {
            return endpoints.Where(e => e.Name.StartsWith("east-"));
        }

        return base.GetEndpointsForGroup(groupName, endpoints);
    }
}
```

以下另一個範例會覆寫預設的協商行為，以選取端點取決於應用程式伺服器的所在位置。

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(HttpContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (endpointName.Count == 0)
        {
            context.Response.StatusCode = 400;
            var response = Encoding.UTF8.GetBytes("Invalid request");
            context.Response.Body.Write(response, 0, response.Length);
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

別忘了使用下列內容向 DI 容器註冊路由器：

```cs
services.AddSingleton(typeof(IEndpointRouter), typeof(CustomRouter));
services.AddSignalR()
        .AddAzureSignalR(
            options => 
            {
                options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
            });
```

## <a name="for-aspnet"></a>針對 ASP.NET

### <a name="how-to-add-multiple-endpoints-from-config"></a>如何從 config 新增多個端點？

具有索引鍵 `Azure:SignalR:ConnectionString` 或 `Azure:SignalR:ConnectionString:` SignalR Service 連接字串的設定。

如果索引鍵開頭為 `Azure:SignalR:ConnectionString:` ，它應該採用格式 `Azure:SignalR:ConnectionString:{Name}:{EndpointType}` ，其中 `Name` 和 `EndpointType` 是物件的屬性 `ServiceEndpoint` ，而且可從程式碼存取。

您可以將多個實例連接字串新增至 `web.config` ：

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <connectionStrings>
    <add name="Azure:SignalR:ConnectionString" connectionString="<ConnectionString1>"/>
    <add name="Azure:SignalR:ConnectionString:en-us" connectionString="<ConnectionString2>"/>
    <add name="Azure:SignalR:ConnectionString:zh-cn:secondary" connectionString="<ConnectionString3>"/>
    <add name="Azure:SignalR:ConnectionString:Backup:secondary" connectionString="<ConnectionString4>"/>
  </connectionStrings>
  ...
</configuration>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>如何從程式碼新增多個端點？

`ServicEndpoint`引進類別來描述 Azure SignalR Service 端點的屬性。
使用 Azure SignalR Service SDK 時，您可以設定多個實例端點：

```cs
app.MapAzureSignalR(
    this.GetType().FullName, 
    options => {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString1>"),
                new ServiceEndpoint("<ConnectionString2>"),
                new ServiceEndpoint("<ConnectionString3>"),
            }
        });
```

### <a name="how-to-customize-router"></a>如何自訂路由器？

ASP.NET SignalR 和 ASP.NET Core SignalR 之間的唯一差異，在於的 HTTP 內容類型 `GetNegotiateEndpoint` 。 針對 ASP.NET SignalR，它是 [IOwinCoNtext](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR.AspNet/EndpointRouters/DefaultEndpointRouter.cs#L19) 類型。

以下是 ASP.NET SignalR 的自訂 negotiate 範例：

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(IOwinContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (string.IsNullOrEmpty(endpointName))
        {
            context.Response.StatusCode = 400;
            context.Response.Write("Invalid request.");
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

別忘了使用下列內容向 DI 容器註冊路由器：

```cs
var hub = new HubConfiguration();
var router = new CustomRouter();
hub.Resolver.Register(typeof(IEndpointRouter), () => router);
app.MapAzureSignalR(GetType().FullName, hub, options => {
    options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
});
```

## <a name="configuration-in-cross-region-scenarios"></a>跨區域案例中的設定

`ServiceEndpoint`物件具有 `EndpointType` 具有值或的屬性 `primary` `secondary` 。

`primary` 端點是接收用戶端流量的慣用端點，並被視為有更可靠的網路連線; `secondary` 端點會被視為具有較不可靠的網路連線，而且只會用來取得伺服器對用戶端的流量，例如，廣播訊息，而不是讓用戶端使用伺服器流量。

在跨區域案例中，網路可能不穩定。 如果是位於 *美國東部*的一個應用程式伺服器，位於相同 *美國東部* 區域的 SignalR Service 端點可以設定為 `primary` ，而其他區域中標記為的端點則可以設定為 `secondary` 。 在此設定中，其他區域中的服務端點可以 **接收** 來自 *美國東部* 應用程式伺服器的訊息，但不會將任何 **跨區域** 用戶端路由傳送到此應用程式伺服器。 架構如下圖所示：

![跨地區基礎](./media/signalr-howto-scale-multi-instances/cross_geo_infra.png)

當用戶端嘗試搭配使用 `/negotiate` 預設路由器的應用程式伺服器時，SDK 會從可用的端點集合中 **隨機選取** 一個端點 `primary` 。 當主要端點無法使用時，SDK 會從所有可用的端點 **隨機選取** `secondary` 。 當伺服器與服務端點之間的連接處於作用中狀態時，端點會標示為 **可用** 。

在跨區域案例中，當用戶端嘗試 `/negotiate` 使用在 *美國東部*託管的應用程式伺服器時，根據預設，它一律會傳回 `primary` 位於相同區域中的端點。 當所有 *美國東部* 端點都無法使用時，用戶端會重新導向至其他區域中的端點。 下方的 [容錯移轉] 區段會詳細說明此案例。

![一般協商](./media/signalr-howto-scale-multi-instances/normal_negotiate.png)

## <a name="fail-over"></a>容錯移轉

當所有 `primary` 端點都無法使用時，用戶端會 `/negotiate` 從可用的 `secondary` 端點挑選。 這種容錯移轉機制要求每個端點都應作為 `primary` 至少一個應用程式伺服器的端點。

![容錯移轉](./media/signalr-howto-scale-multi-instances/failover_negotiate.png)

## <a name="next-steps"></a>接下來的步驟

在本指南中，您已瞭解如何在相同的應用程式中設定多個實例，以進行調整、分區化和跨區域案例。

多個端點支援也可用於高可用性和嚴重損壞修復案例。

> [!div class="nextstepaction"]
> [針對嚴重損壞修復和高可用性設定 SignalR Service](./signalr-concept-disaster-recovery.md)

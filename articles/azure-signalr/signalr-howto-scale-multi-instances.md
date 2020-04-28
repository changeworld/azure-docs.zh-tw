---
title: 使用多個實例進行調整-Azure SignalR Service
description: 在許多調整案例中，客戶通常需要布建多個實例，並將其設定為一起使用，以建立大規模部署。 例如，分區化需要多個實例支援。
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: zhshang
ms.openlocfilehash: 43d703312cbc1fc067a2d51d5623ed028ba01405
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74158168"
---
# <a name="how-to-scale-signalr-service-with-multiple-instances"></a>如何使用多個實例來調整 SignalR Service？
最新的 SignalR Service SDK 支援 SignalR Service 實例的多個端點。 您可以使用這項功能來調整並行連接，或用於跨區域訊息。

## <a name="for-aspnet-core"></a>針對 ASP.NET Core

### <a name="how-to-add-multiple-endpoints-from-config"></a>如何從 config 新增多個端點？

具有索引鍵`Azure:SignalR:ConnectionString`的`Azure:SignalR:ConnectionString:` Config，或用於 SignalR Service 連接字串的設定。

如果索引鍵的開頭`Azure:SignalR:ConnectionString:`為，其格式`Azure:SignalR:ConnectionString:{Name}:{EndpointType}`應該是，其中`Name`和`EndpointType`是`ServiceEndpoint`物件的屬性，而且可以從程式碼存取。

您可以使用下列`dotnet`命令來新增多個實例連接字串：

```batch
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-a <ConnectionString1>
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-b:primary <ConnectionString2>
dotnet user-secrets set Azure:SignalR:ConnectionString:backup:secondary <ConnectionString3>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>如何從程式碼新增多個端點？

引進`ServicEndpoint`類別來描述 Azure SignalR Service 端點的屬性。
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

根據預設，SDK 會使用[DefaultEndpointRouter](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/EndpointRouters/DefaultEndpointRouter.cs)來挑選端點。

#### <a name="default-behavior"></a>預設行為 
1. 用戶端要求路由

    當客戶`/negotiate`端與應用程式伺服器搭配使用時。 根據預設，SDK 會從可用的服務端點集合中**隨機選取**一個端點。

2. 伺服器訊息路由

    當 * 傳送訊息至特定的 * * 連接 * * *，而目標連接路由至目前的伺服器時，訊息就會直接移至該已連線的端點。 否則，訊息會廣播到每個 Azure SignalR 端點。

#### <a name="customize-routing-algorithm"></a>自訂路由演算法
當您有特殊知識來識別訊息應該移至哪些端點時，您可以建立自己的路由器。

以下定義自訂路由器做為範例，當群組的`east-`開頭為一律移至名為`east`的端點時：

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

以下另一個範例會覆寫預設的 negotiate 行為，以選取端點取決於應用程式伺服器所在的位置。

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

別忘了使用下列步驟，將路由器註冊到 DI 容器：

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

具有索引鍵`Azure:SignalR:ConnectionString`的`Azure:SignalR:ConnectionString:` Config，或用於 SignalR Service 連接字串的設定。

如果索引鍵的開頭`Azure:SignalR:ConnectionString:`為，其格式`Azure:SignalR:ConnectionString:{Name}:{EndpointType}`應該是，其中`Name`和`EndpointType`是`ServiceEndpoint`物件的屬性，而且可以從程式碼存取。

您可以將多個實例連接字串`web.config`新增至：

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

引進`ServicEndpoint`類別來描述 Azure SignalR Service 端點的屬性。
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

ASP.NET SignalR 和 ASP.NET Core SignalR 之間唯一的差異是的 HTTP 內容類型`GetNegotiateEndpoint`。 若為 ASP.NET SignalR，則為[IOwinCoNtext](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR.AspNet/EndpointRouters/DefaultEndpointRouter.cs#L19)類型。

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

別忘了使用下列步驟，將路由器註冊到 DI 容器：

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

`ServiceEndpoint`物件具有值`primary`為`EndpointType`或`secondary`的屬性。

`primary`端點是接收用戶端流量的慣用端點，並被視為具有更可靠的網路連線;`secondary`端點會被視為具有較不可靠的網路連線，而且只會用於讓伺服器進入用戶端流量，例如廣播訊息，而不是讓用戶端進行伺服器流量。

在跨區域的情況下，網路可能不穩定。 針對位於*美國東部*的一個應用程式伺服器，位於相同*美國東部*區域的 SignalR Service 端點可以設定為`primary` ，而其他區域中的端點會標示`secondary`為。 在此設定中，其他區域中的服務端點可以**接收**來自此「*美國東部*」應用程式伺服器的訊息，但不會有任何**跨區域**用戶端路由傳送到此應用程式伺服器。 下圖顯示此架構：

![跨地區基礎](./media/signalr-howto-scale-multi-instances/cross_geo_infra.png)

當用戶端嘗試`/negotiate`使用預設路由器的應用程式伺服器時，SDK 會從可用`primary`的端點集合中**隨機選取**一個端點。 當主要端點無法使用時，SDK 會從所有可用`secondary`的端點**隨機選取**。 當伺服器與服務端點之間的連線處於作用中狀態時，端點會標示為**可用**。

在跨區域案例中，當用戶端嘗試`/negotiate`使用位於*美國東部*的應用程式伺服器時，依預設，它一律`primary`會傳回位於相同區域中的端點。 當所有「*美國東部*」端點都無法使用時，會將用戶端重新導向至其他區域中的端點。 下一節會詳細說明案例。

![標準談判](./media/signalr-howto-scale-multi-instances/normal_negotiate.png)

## <a name="fail-over"></a>故障切換

當所有`primary`端點都無法使用時，客戶`/negotiate`端會從可用`secondary`的端點挑選。 這種故障傳遞機制會要求每個端點至少應`primary`作為一個應用程式伺服器的端點。

![故障切換](./media/signalr-howto-scale-multi-instances/failover_negotiate.png)

## <a name="next-steps"></a>後續步驟

在本指南中，您已瞭解如何在相同的應用程式中設定多個實例，以進行調整、分區化和跨區域的案例。

多個端點支援也可以在高可用性和嚴重損壞修復案例中使用。

> [!div class="nextstepaction"]
> [設定 SignalR Service 以進行嚴重損壞修復和高可用性](./signalr-concept-disaster-recovery.md)

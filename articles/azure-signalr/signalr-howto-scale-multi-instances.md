---
title: 使用多個實例進行縮放 - Azure 信號R 服務
description: 在許多縮放方案中，客戶通常需要預配多個實例並配置以將它們一起使用，以創建大規模部署。 例如，分片需要多個實例支援。
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: zhshang
ms.openlocfilehash: 43d703312cbc1fc067a2d51d5623ed028ba01405
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74158168"
---
# <a name="how-to-scale-signalr-service-with-multiple-instances"></a>如何使用多個實例擴展 SignalR 服務？
最新的 SignalR 服務 SDK 支援 SignalR 服務實例的多個終結點。 可以使用此功能縮放併發連接，或將其用於跨區域消息傳遞。

## <a name="for-aspnet-core"></a>用於ASP.NET核心

### <a name="how-to-add-multiple-endpoints-from-config"></a>如何從配置中添加多個終結點？

使用鍵`Azure:SignalR:ConnectionString`或`Azure:SignalR:ConnectionString:`SignalR 服務連接字串進行配置。

如果鍵以`Azure:SignalR:ConnectionString:``Azure:SignalR:ConnectionString:{Name}:{EndpointType}`開頭，則應採用 格式，其中`Name`和`EndpointType`是`ServiceEndpoint`物件的屬性，並且可從代碼訪問。

您可以使用以下`dotnet`命令添加多個實例連接字串：

```batch
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-a <ConnectionString1>
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-b:primary <ConnectionString2>
dotnet user-secrets set Azure:SignalR:ConnectionString:backup:secondary <ConnectionString3>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>如何從代碼中添加多個終結點？

引入了`ServicEndpoint`一個類來描述 Azure SignalR 服務終結點的屬性。
在使用 Azure SignalR 服務 SDK 時，可以通過以下版本配置多個實例終結點：
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

預設情況下，SDK 使用[預設終結點路由器](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/EndpointRouters/DefaultEndpointRouter.cs)拾取終結點。

#### <a name="default-behavior"></a>預設行為 
1. 用戶端請求路由

    當用戶端`/negotiate`與應用伺服器。 預設情況下，SDK 從一組可用服務終結點**中隨機播放**一個終結點。

2. 伺服器消息路由

    當 [將消息發送到特定 [連接]，並且目標連接路由到當前伺服器時，消息將直接發送到該連接的終結點。 否則，消息將廣播到每個 Azure SignalR 終結點。

#### <a name="customize-routing-algorithm"></a>自訂路由演算法
當您有特殊知識來標識消息應轉到哪些終結點時，您可以創建自己的路由器。

以下定義自訂路由器作為示例，當從 開始組的`east-`組始終轉到名為 的`east`終結點時：

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

下面的另一個示例，用於覆蓋預設協商行為，以選擇終結點取決於應用伺服器的位置。

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

不要忘記使用以下功能將路由器註冊到 DI 容器：

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

## <a name="for-aspnet"></a>對於ASP.NET

### <a name="how-to-add-multiple-endpoints-from-config"></a>如何從配置中添加多個終結點？

使用鍵`Azure:SignalR:ConnectionString`或`Azure:SignalR:ConnectionString:`SignalR 服務連接字串進行配置。

如果鍵以`Azure:SignalR:ConnectionString:``Azure:SignalR:ConnectionString:{Name}:{EndpointType}`開頭，則應採用 格式，其中`Name`和`EndpointType`是`ServiceEndpoint`物件的屬性，並且可從代碼訪問。

您可以將多個實例連接字串添加到`web.config`：

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

### <a name="how-to-add-multiple-endpoints-from-code"></a>如何從代碼中添加多個終結點？

引入了`ServicEndpoint`一個類來描述 Azure SignalR 服務終結點的屬性。
在使用 Azure SignalR 服務 SDK 時，可以通過以下版本配置多個實例終結點：

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

ASP.NET信號R和ASP.NET核心信號R的唯一區別是`GetNegotiateEndpoint`的 HTTP 上下文類型。 對於ASP.NET信號R，它是[IOwinCoNtext](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR.AspNet/EndpointRouters/DefaultEndpointRouter.cs#L19)類型。

下面是ASP.NET信號R的自訂協商示例：

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

不要忘記使用以下功能將路由器註冊到 DI 容器：

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

## <a name="configuration-in-cross-region-scenarios"></a>跨區域方案中的配置

物件`ServiceEndpoint`具有值`EndpointType``primary`或`secondary`的屬性。

`primary`終結點是接收用戶端流量的首選終結點，並且被認為具有更可靠的網路連接;`secondary`終結點被認為具有不太可靠的網路連接，僅用於將伺服器帶到用戶端流量，例如廣播訊息，而不是將用戶端帶到伺服器流量。

在跨區域情況下，網路可能不穩定。 對於位於*美國東部的*一個應用伺服器，位於*美國東部*同一區域的 SignalR 服務終結點可以配置為`primary`和終結點在標記為`secondary`的其他地區。 在此配置中，其他地區的服務終結點可以**接收**來自此*東美國*應用伺服器的消息，但不會將**跨區域**用戶端路由到此應用伺服器。 體系結構如下圖所示：

![橫貫地](./media/signalr-howto-scale-multi-instances/cross_geo_infra.png)

當用戶端使用應用`/negotiate`伺服器（使用預設路由器）嘗試時，SDK 會從一組可用`primary`終結點**中隨機播放**一個終結點。 當主終結點無法接通，SDK 然後從所有可用`secondary`終結點**隨機播放**。 當伺服器和服務終結點之間的連接處於活動狀態時，終結點被標記為**可用**。

在跨區域方案中，當用戶端嘗試`/negotiate`使用託管*在美國東部*的應用伺服器時，預設情況下，它始終返回位於同一區域`primary`中的終結點。 當所有*東美國*終結點無法接通，用戶端將重定向到其他區域中的終結點。 下面的容錯移轉部分詳細介紹了方案。

![正常談判](./media/signalr-howto-scale-multi-instances/normal_negotiate.png)

## <a name="fail-over"></a>容錯移轉

當所有`primary`終結點無法接通，用戶端從`/negotiate`可用`secondary`終結點選擇。 此容錯移轉機制要求每個終結點應作為`primary`至少一個應用伺服器的終結點。

![容錯移轉](./media/signalr-howto-scale-multi-instances/failover_negotiate.png)

## <a name="next-steps"></a>後續步驟

在本指南中，您瞭解了如何在同一應用程式中配置多個實例以進行縮放、分片和跨區域方案。

多個終結點支援也可用於高可用性和災害復原方案。

> [!div class="nextstepaction"]
> [設置信號器服務，用於災害復原和高可用性](./signalr-concept-disaster-recovery.md)

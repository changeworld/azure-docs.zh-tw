---
title: 即時串流 Azure Spring Cloud 應用程式記錄
description: 如何使用日誌流即時查看應用程式日誌
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/14/2019
ms.openlocfilehash: fc208a3542528fb4554a365a02e13c2da3055cf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78192195"
---
# <a name="stream-azure-spring-cloud-app-logs-in-real-time"></a>即時串流 Azure Spring Cloud 應用程式記錄
Azure Spring Cloud 使 Azure CLI 中的日誌流獲取用於故障排除的即時應用程式主控台日誌。 您還可以[使用診斷設置分析日誌和指標](./diagnostic-services.md)。

## <a name="prerequisites"></a>Prerequisites

* 安裝春雲的[Azure CLI 擴展，](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli#install-the-azure-cli-extension)最小版本 0.2.0 。
* 具有正在運行的應用程式的**Azure 春雲**實例，例如[Spring Cloud 應用](./spring-cloud-quickstart-launch-app-cli.md)。

> [!NOTE]
>  ASC CLI 擴展從版本 0.2.0 更新為 0.2.1。 此更改會影響日誌流命令的語法： `az spring-cloud app log tail`， 替換為 ： `az spring-cloud app logs`。 命令：`az spring-cloud app log tail`將在將來的版本中棄用。 如果您一直在使用版本 0.2.0，則可以升級到 0.2.1。 首先，使用命令刪除舊版本： `az extension remove -n spring-cloud`。  然後，按命令安裝 0.2.1： `az extension add -n spring-cloud`。

## <a name="use-cli-to-tail-logs"></a>使用 CLI 跟蹤日誌

為避免重複指定資源組和服務實例名稱，請設置預設資源組名稱和群集名稱。
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```
在以下示例中，將在命令中省略資源組和服務名稱。

### <a name="tail-log-for-app-with-single-instance"></a>具有單個實例的應用的尾日誌
如果名為 auth-service 的應用只有一個實例，則可以使用以下命令查看應用實例的日誌：
```
az spring-cloud app logs -n auth-service
```
這將返回日誌：
```
...
2020-01-15 01:54:40.481  INFO [auth-service,,,] 1 --- [main] o.apache.catalina.core.StandardService  : Starting service [Tomcat]
2020-01-15 01:54:40.482  INFO [auth-service,,,] 1 --- [main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.22]
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.a.c.c.C.[Tomcat].[localhost].[/uaa]  : Initializing Spring embedded WebApplicationContext
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.s.web.context.ContextLoader  : Root WebApplicationContext: initialization completed in 7203 ms

...
```

### <a name="tail-log-for-app-with-multiple-instances"></a>具有多個實例的應用的尾日誌
如果名為`auth-service`的應用存在多個實例，則可以使用 選項`-i/--instance`查看實例日誌。 

首先，可以使用以下命令獲取應用實例名稱。

```
az spring-cloud app show -n auth-service --query properties.activeDeployment.properties.instances -o table
```
結果：

```
Name                                         Status    DiscoveryStatus
-------------------------------------------  --------  -----------------
auth-service-default-12-75cc4577fc-pw7hb  Running   UP
auth-service-default-12-75cc4577fc-8nt4m  Running   UP
auth-service-default-12-75cc4577fc-n25mh  Running   UP
``` 
然後，您可以使用選項`-i/--instance`流應用實例的日誌：

```
az spring-cloud app logs -n auth-service -i auth-service-default-12-75cc4577fc-pw7hb
```

您還可以從 Azure 門戶獲取應用實例的詳細資訊。  在 Azure Spring 雲服務服務的左側功能窗格中選擇**應用**後，選擇**應用實例**。

### <a name="continuously-stream-new-logs"></a>持續資料流新日誌
預設情況下，`az spring-cloud ap log tail`僅列印資料流到應用主控台的現有日誌，然後退出。 如果要資料流新日誌，請添加 -f （-關注）：  

```
az spring-cloud app logs -n auth-service -f
``` 
要檢查所有支援的日誌記錄選項，請進行以下操作：
``` 
az spring-cloud app logs -h 
```

## <a name="next-steps"></a>後續步驟

* [使用診斷設置分析日誌和指標](./diagnostic-services.md)

 






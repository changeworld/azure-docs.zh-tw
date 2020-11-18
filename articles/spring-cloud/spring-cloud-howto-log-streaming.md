---
title: 即時串流 Azure Spring Cloud 應用程式記錄
description: 如何使用記錄串流立即查看應用程式記錄檔
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/14/2019
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: fcfddce568be6c641a5bf5be70c2cd0ad368095f
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843599"
---
# <a name="stream-azure-spring-cloud-app-logs-in-real-time"></a>即時串流 Azure Spring Cloud 應用程式記錄

**本文適用於：** ✔️ Java ✔️ C#

Azure 春季 Cloud 可在 Azure CLI 中進行記錄串流，以取得即時應用程式主控台記錄以進行疑難排解。 您也可以 [使用診斷設定來分析記錄和計量](./diagnostic-services.md)。

## <a name="prerequisites"></a>先決條件

* 安裝適用于春季雲端的 [Azure CLI 擴充](/cli/azure/install-azure-cli) 功能，最小版本0.2.0。
* 具有正在執行之應用程式的 **Azure 春季雲端** 實例，例如 [春季 cloud app](./spring-cloud-quickstart.md)。

> [!NOTE]
>  ASC CLI 擴充功能已從版本0.2.0 更新為0.2.1。 這項變更會影響記錄檔資料流程的命令語法： `az spring-cloud app log tail` ，取代為： `az spring-cloud app logs` 。 命令： `az spring-cloud app log tail` 將在未來的版本中被取代。 如果您使用的是版本0.2.0，可以升級至0.2.1。 首先，使用下列命令來移除舊版本： `az extension remove -n spring-cloud` 。  然後，透過命令安裝0.2.1： `az extension add -n spring-cloud` 。

## <a name="use-cli-to-tail-logs"></a>使用 CLI 來結尾記錄

若要避免重複指定資源群組和服務實例名稱，請設定您的預設資源組名和叢集名稱。
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```
在下列範例中，命令中會省略資源群組和服務名稱。

### <a name="tail-log-for-app-with-single-instance"></a>具有單一實例的應用程式尾日誌
如果名為 auth service 的應用程式只有一個實例，您可以使用下列命令來查看應用程式實例的記錄：
```
az spring-cloud app logs -n auth-service
```
這會傳回記錄：
```
...
2020-01-15 01:54:40.481  INFO [auth-service,,,] 1 --- [main] o.apache.catalina.core.StandardService  : Starting service [Tomcat]
2020-01-15 01:54:40.482  INFO [auth-service,,,] 1 --- [main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.22]
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.a.c.c.C.[Tomcat].[localhost].[/uaa]  : Initializing Spring embedded WebApplicationContext
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.s.web.context.ContextLoader  : Root WebApplicationContext: initialization completed in 7203 ms

...
```

### <a name="tail-log-for-app-with-multiple-instances"></a>具有多個實例的應用程式尾日誌
如果名為的應用程式有多個實例 `auth-service` ，您可以使用選項來查看實例記錄檔 `-i/--instance` 。 

首先，您可以使用下列命令來取得應用程式實例名稱。

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
然後，您可以使用選項選項來串流應用程式實例的記錄 `-i/--instance` ：

```
az spring-cloud app logs -n auth-service -i auth-service-default-12-75cc4577fc-pw7hb
```

您也可以從 Azure 入口網站取得應用程式實例的詳細資料。  在 Azure 春季雲端服務的左側流覽窗格中選取 **應用程式** 之後，請選取 [ **應用程式實例**]。

### <a name="continuously-stream-new-logs"></a>持續串流新的記錄
依預設， `az spring-cloud ap log tail` 只會列印串流至應用程式主控台的現有記錄，然後結束。 如果您想要串流新的記錄檔，請新增-f (--請遵循) ：  

```
az spring-cloud app logs -n auth-service -f
``` 
若要檢查所有支援的記錄選項：
``` 
az spring-cloud app logs -h 
```

## <a name="next-steps"></a>後續步驟
* [快速入門：使用記錄、計量和追蹤來監視 Azure Spring Cloud 應用程式](spring-cloud-quickstart-logs-metrics-tracing.md)
* [使用診斷設定來分析記錄和計量](./diagnostic-services.md)


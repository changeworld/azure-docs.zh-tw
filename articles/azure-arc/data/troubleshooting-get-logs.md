---
title: 取得記錄以針對 Azure Arc 啟用的資料控制站進行疑難排解
description: 取得服務記錄檔，以針對 Azure Arc 啟用的資料控制器進行疑難排解。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 625092e0557d40051e1ffd538a496c20edc0222f
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92320209"
---
# <a name="get-azure-arc-enabled-data-services-logs"></a>取得 Azure Arc 啟用的資料服務記錄

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>先決條件

繼續進行之前，您需要：

* [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]. [安裝指示](./install-client-tools.md)。
* 用以登入 Azure Arc 啟用的資料服務控制器的系統管理員帳戶。

## <a name="get-azure-arc-enabled-data-services-logs"></a>取得 Azure Arc 啟用的資料服務記錄

您可以在所有 pod 或特定 pod 上取得 Azure Arc 啟用的資料服務記錄檔，以供疑難排解之用。 您可以使用標準 Kubernetes 工具（例如命令）來執行這 `kubectl logs` 項作業，或者在本文中，您將使用 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 工具，這可讓您更輕鬆地取得所有記錄檔。

1. 使用系統管理員帳戶登入資料控制器。

   ```console
   azdata login
   ```

2. 執行下列命令來傾印記錄：

   ```console
   azdata arc dc debug copy-logs --namespace <namespace name> --exclude-dumps --skip-compress
   ```

   例如：

   ```console
   #azdata arc dc debug copy-logs --namespace arc --exclude-dumps --skip-compress
   ```

資料控制器會在名為的子目錄中建立目前工作目錄中的記錄檔 `logs` 。 

## <a name="options"></a>選項

`azdata arc dc debug copy-logs` 提供下列選項來管理輸出。

* 使用參數將記錄檔輸出至不同的目錄 `--target-folder` 。
* 藉由省略參數來壓縮檔案 `--skip-compress` 。
* 藉由省略來觸發和包含記憶體傾印 `--exclude-dumps` 。 除非 Microsoft 支援服務已要求記憶體傾印，否則不建議使用此方法。 若要建立記憶體傾印，必須將資料控制器設定 `allowDumps` 設定為 `true` 資料控制器建立的時間。
* 篩選以 `--pod` 依名稱 () 或容器 () 收集特定 pod 的記錄 `--container` 。
* 藉由傳遞和參數，篩選以收集特定自訂資源的記錄 `--resource-kind` `--resource-name` 。 `resource-kind`參數值應該是可由命令抓取的其中一個自訂資源定義名稱 `kubectl get customresourcedefinition` 。

您可以使用這些參數來取代 `<parameters>` 下列範例中的。 

```console
azdata arc dc debug copy-logs --target-folder <desired folder> --exclude-dumps --skip-compress -resource-kind <custom resource definition name> --resource-name <resource name> --namespace <namespace name>
```

例如：

```console
#azdata arc dc debug copy-logs --target-folder C:\temp\logs --exclude-dumps --skip-compress --resource-kind postgresql-12 --resource-name pg1 --namespace arc
```

資料夾階層的範例。 資料夾階層會依 pod 名稱、容器，以及容器內的目錄階層進行組織。

```output
<export directory>
├───debuglogs-arc-20200827-180403
│   ├───bootstrapper-vl8j2
│   │   └───bootstrapper
│   │       ├───apt
│   │       └───fsck
│   ├───control-j2dm5
│   │   ├───controller
│   │   │   └───controller
│   │   │       ├───2020-08-27
│   │   │       └───2020-08-28
│   │   └───fluentbit
│   │       ├───agent
│   │       ├───fluentbit
│   │       └───supervisor
│   │           └───log
│   ├───controldb-0
│   │   ├───fluentbit
│   │   │   ├───agent
│   │   │   ├───fluentbit
│   │   │   └───supervisor
│   │   │       └───log
│   │   └───mssql-server
│   │       ├───agent
│   │       ├───mssql
│   │       ├───mssql-server
│   │       └───supervisor
│   │           └───log
│   ├───controlwd-ln6j8
│   │   └───controlwatchdog
│   │       └───controlwatchdog
│   ├───logsdb-0
│   │   └───elasticsearch
│   │       ├───agent
│   │       ├───elasticsearch
│   │       ├───provisioner
│   │       └───supervisor
│   │           └───log
│   ├───logsui-7gg2d
│   │   └───kibana
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───kibana
│   │       └───supervisor
│   │           └───log
│   ├───metricsdb-0
│   │   └───influxdb
│   │       ├───agent
│   │       ├───influxdb
│   │       └───supervisor
│   │           └───log
│   ├───metricsdc-2f62t
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-jznd2
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-n5vnx
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsui-h748h
│   │   └───grafana
│   │       ├───agent
│   │       ├───grafana
│   │       └───supervisor
│   │           └───log
│   └───mgmtproxy-r5zxs
│       ├───fluentbit
│       │   ├───agent
│       │   ├───fluentbit
│       │   └───supervisor
│       │       └───log
│       └───service-proxy
│           ├───agent
│           ├───nginx
│           └───supervisor
│               └───log
└───debuglogs-kube-system-20200827-180431
    ├───coredns-8bbb65c89-kklt7
    │   └───coredns
    ├───coredns-8bbb65c89-z2vvr
    │   └───coredns
    ├───coredns-autoscaler-5585bf8c9f-g52nt
    │   └───autoscaler
    ├───kube-proxy-5c9s2
    │   └───kube-proxy
    ├───kube-proxy-h6x56
    │   └───kube-proxy
    ├───kube-proxy-nd2b7
    │   └───kube-proxy
    ├───metrics-server-5f54b8994-vpm5r
    │   └───metrics-server
    └───tunnelfront-db87f4cd8-5xwxv
        ├───tunnel-front
        │   ├───apt
        │   └───journal
        └───tunnel-probe
            ├───apt
            ├───journal
            └───openvpn
```

## <a name="next-steps"></a>後續步驟

[azdata arc dc debug copy-logs](/sql/azdata/reference/reference-azdata-arc-dc-debug#azdata-arc-dc-debug-copy-logs?toc=/azure/azure-arc/data/toc.json&bc=/azure/azure-arc/data/breadcrumb/toc.json)
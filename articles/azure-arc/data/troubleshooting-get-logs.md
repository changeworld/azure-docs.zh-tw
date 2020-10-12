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
ms.openlocfilehash: 71c84b35c001be7fafdc2df53014050ae21dec63
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934322"
---
# <a name="get-azure-arc-enabled-data-services-logs"></a>取得 Azure Arc 啟用的資料服務記錄

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>必要條件

若要取得已啟用 Azure Arc 的資料服務記錄，您將需要 Azure Data CLI 工具。 [安裝指示](./install-client-tools.md)

您將必須能夠以系統管理員身分登入 Azure Arc 啟用的資料服務控制器服務。

## <a name="get-azure-arc-enabled-data-services-logs"></a>取得 Azure Arc 啟用的資料服務記錄

您可以在所有 pod 或特定 pod 上取得 Azure Arc 啟用的資料服務記錄檔，以供疑難排解之用。  您可以使用標準 Kubernetes 工具（例如命令）來執行這項作業， `kubectl logs` 或者在本文中，您將使用 Azure DATA CLI 工具，讓您可以更輕鬆地同時取得所有記錄。

首先，請確定您已登入資料控制站。

```console
azdata login
```

然後執行下列命令來傾印記錄：
```console
azdata arc dc debug copy-logs --namespace <namespace name> --exclude-dumps --skip-compress

#Example:
#azdata arc dc debug copy-logs --namespace arc --exclude-dumps --skip-compress
```

記錄檔預設會在名為 ' logs ' 的子目錄中建立在目前的工作目錄中。  您可以使用參數將記錄檔輸出至不同的目錄 `--target-folder` 。

您可以選擇省略參數來壓縮檔案 `--skip-compress` 。

您可以藉由省略來觸發和包含記憶體傾印 `--exclude-dumps` ，但除非 Microsoft 支援服務已要求記憶體傾印，否則不建議這麼做。  若要建立記憶體傾印，必須將資料控制器設定 `allowDumps` 設定為 `true` 資料控制器建立的時間。

您可以選擇性地選擇進行篩選，以 `--pod` 依名稱 () 或容器 () 收集特定 pod 的記錄 `--container` 。

您也可以透過傳遞和辨識，選擇篩選以收集特定自訂資源的 `--resource-kind` 記錄 `--resource-name` 。  `resource-kind`參數值應該是可由命令抓取的其中一個自訂資源定義名稱 `kubectl get customresourcedefinition` 。

```console
azdata arc dc debug copy-logs --target-folder <desired folder> --exclude-dumps --skip-compress -resource-kind <custom resource definition name> --resource-name <resource name> --namespace <namespace name>

#Example
#azdata arc dc debug copy-logs --target-folder C:\temp\logs --exclude-dumps --skip-compress --resource-kind postgresql-12 --resource-name pg1 --namespace arc
```

資料夾階層的範例。  請注意，資料夾階層是依 pod 名稱名稱以及容器內的目錄階層來組織。

```console
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
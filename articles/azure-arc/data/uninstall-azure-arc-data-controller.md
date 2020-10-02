---
title: 刪除 Azure Arc 資料控制器
description: 刪除 Azure Arc 資料控制器
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 7e1a2d50e04601b977bb7a708f60e78089ddded1
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631013"
---
# <a name="delete-azure-arc-data-controller"></a>刪除 Azure Arc 資料控制器

下列文章說明如何刪除 Azure Arc 資料控制器。

繼續之前，請確定已在資料控制器上建立的所有資料服務都已移除，如下所示：

## <a name="log-in-to-the-data-controller"></a>登入資料控制器

登入您想要刪除的資料控制器：

```
azdata login
```

## <a name="list--delete-existing-data-services"></a>列出 & 刪除現有的資料服務

執行下列命令來檢查是否已建立任何 SQL 受控實例：

```
azdata arc sql mi list
```

針對上述清單中的每個 SQL 受控實例，執行 delete 命令，如下所示：

```
azdata arc sql mi delete -n <name>
# for example: azdata arc sql mi delete -n sqlinstance1
```

同樣地，若要檢查于 postgresql 超大規模實例，請執行：

```
azdata arc postgres server list
```

針對每個于 postgresql 超大規模實例，執行 delete 命令，如下所示：
```
azdata arc postgres server delete -n <name>
# for example: azdata arc postgres server delete -n pg1
```

## <a name="delete-controller"></a>刪除控制器

移除所有 SQL 受控實例和于 postgresql 超大規模實例之後，就可以刪除資料控制器，如下所示：

```
azdata arc dc delete -n <name> -ns <namespace>
# for example: azdata arc dc delete -ns arc -n arcdc
```

### <a name="remove-sccs-red-hat-openshift-only"></a>移除 SCCs (Red Hat OpenShift only) 

```console
oc adm policy remove-scc-from-user privileged -z default -n arc
oc adm policy remove-scc-from-user anyuid     -z default -n arc
```

### <a name="delete-cluster-level-objects"></a>刪除叢集層級物件

除了命名空間範圍的物件之外，如果您也想要刪除叢集層級物件，例如 CRDs、 `clusterroles` 和，請 `clusterrolebindings` 執行下列命令：

```
# Cleanup azure arc data service artifacts
kubectl delete crd datacontrollers.arcdata.microsoft.com 
kubectl delete crd sqlmanagedinstances.sql.arcdata.microsoft.com 
kubectl delete crd postgresql-11s.arcdata.microsoft.com 
kubectl delete crd postgresql-12s.arcdata.microsoft.com
kubectl delete clusterroles azure-arc-data:cr-arc-metricsdc-reader
kubectl delete clusterrolebindings azure-arc-data:crb-arc-metricsdc-reader
```

### <a name="optionally-delete-the-azure-arc-data-controller-namespace"></a>（選擇性）刪除 Azure Arc 資料控制器命名空間


```console
kubectl delete ns <nameSpecifiedDuringCreation>
# for example kubectl delete ns arc
```

## <a name="next-steps"></a>後續步驟

[哪些 Azure Arc 啟用的資料服務？](overview.md)

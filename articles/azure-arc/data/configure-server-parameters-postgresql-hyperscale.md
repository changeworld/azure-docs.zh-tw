---
title: 在 Azure Arc 上為您的于 postgresql 超大規模伺服器群組設定 Postgres 引擎伺服器參數
titleSuffix: Azure Arc enabled data services
description: 在 Azure Arc 上為您的于 postgresql 超大規模伺服器群組設定 Postgres 引擎伺服器參數
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: cdbddfc84b3f71576cfd0299f2babec859b4ef1f
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92311053"
---
# <a name="set-the-database-engine-settings-for-azure-arc-enabled-postgresql-hyperscale"></a>為已啟用 Azure Arc 的 PostgreSQL 超大規模資料庫設定資料庫引擎設定

本文件說明用來將 PostgreSQL 超大規模資料庫伺服器群組的資料庫引擎設定設為自訂 (非預設) 值的步驟。 如需可設定哪些資料庫引擎參數及其預設值的詳細資訊，請參閱 [此處](https://www.postgresql.org/docs/current/runtime-config.html)的于 postgresql 檔。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

> [!NOTE]
> 預覽不支援設定下列參數： 
>
> - `archive_command`
> - `archive_timeout`
> - `log_directory`
> - `log_file_mode`
> - `log_filename`
> - `restore_command`
> - `shared_preload_libraries`
> - `synchronous_commit`
> - `ssl`
> - `wal_level`

## <a name="syntax"></a>語法

設定 database engine 設定的命令一般格式為：

```console
azdata arc postgres server edit -n <server group name>, [{--engine-settings, -e}] [{--replace-engine-settings, --re}] {'<parameter name>=<parameter value>, ...'}
```

## <a name="show-current-custom-values"></a>顯示目前的自訂值

### <a name="with-azure-data-cli-azdata-command"></a>With [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 命令

```console
azdata arc postgres server show -n <server group name>
```

例如：

```console
azdata arc postgres server show -n postgres01
```

此命令會傳回您要在其中看到所設定參數的伺服器群組規格。 如果沒有 engine\settings 區段，則表示所有參數都是在其預設值上執行：

```console
 "
...
engine": {
      "settings": {
        "default": {
          "autovacuum_vacuum_threshold": "65"
        }
      }
    },
...
```

### <a name="with-kubectl-command"></a>With kubectl 命令

遵循下列步驟。

1. 為您的伺服器群組取出自訂資源定義的種類

   執行：

   ```console
   azdata arc postgres server show -n <server group name>
   ```

   例如：

   ```console
   azdata arc postgres server show -n postgres01
   ```

   此命令會傳回您要在其中看到所設定參數的伺服器群組規格。 如果沒有 engine\settings 區段，則表示所有參數都是在其預設值上執行：

   ```output
   > {
     >"apiVersion": "arcdata.microsoft.com/v1alpha1",
     >"**kind**": "**postgresql-12**",
     >"metadata": {
       >"creationTimestamp": "2020-08-25T14:32:23Z",
       >"generation": 1,
       >"name": "postgres01",
       >"namespace": "arc",  
   ```

   在輸出結果中，尋找欄位 `kind` 並將值設為旁，例如： `postgresql-12` 。

2. 描述對應到您的伺服器群組的 Kubernetes 自訂資源 

   命令的一般格式為：

   ```console
   kubectl describe <kind of the custom resource> <server group name> -n <namespace name>
   ```

   例如：

   ```console
   kubectl describe postgresql-12 postgres01
   ```

   如果有為引擎設定設定的自訂值，則會傳回它們。 例如：

   ```output
   Engine:
   ...
    Settings:
      Default:
        autovacuum_vacuum_threshold:  65
   ```

   如果您未設定任何引擎設定的自訂值，的 [引擎設定] 區段將會 `resultset` 是空的，如下所示：

   ```output
   Engine:
   ...
       Settings:
         Default:
   ```

## <a name="set-custom-values-for-engine-settings"></a>設定引擎設定的自訂值

下列命令會將協調器節點的參數和于 postgresql 超大規模的背景工作角色節點設定為相同的值。 您還無法針對伺服器群組中的每個角色設定參數。 也就是說，您還無法將指定的參數設定為協調器節點上的特定參數，也可以設定為背景工作節點的另一個值。

### <a name="set-a-single-parameter"></a>設定單一參數

```console
azdata arc server edit -n <server group name> -e <parameter name>=<parameter value>
```

例如：

```console
azdata arc postgres server edit -n postgres01 -e shared_buffers=8MB
```

### <a name="set-multiple-parameters-with-a-single-command"></a>使用單一命令來設定多個參數

```console
azdata arc postgres server edit -n <server group name> -e '<parameter name>=<parameter value>, <parameter name>=<parameter value>,...'
```

例如：

```console
azdata arc postgres server edit -n postgres01 -e 'shared_buffers=8MB, max_connections=50'
```

### <a name="reset-a-parameter-to-its-default-value"></a>將參數重設為其預設值

若要將參數重設為預設值，請在不指出值的情況下加以設定。 

例如：

```console
azdata arc postgres server edit -n postgres01 -e shared_buffers=
```

### <a name="reset-all-parameters-to-their-default-values"></a>將所有參數重設為預設值

```console
azdata arc postgres server edit -n <server group name> -e '' -re
```

例如：

```console
azdata arc postgres server edit -n postgres01 -e '' -re
```

## <a name="special-considerations"></a>特殊考量

### <a name="set-a-parameter-which-value-contains-a-comma-space-or-special-character"></a>設定參數，其中值包含逗號、空格或特殊字元

```console
azdata arc postgres server edit -n <server group name> -e '<parameter name>="<parameter value>"'
```

例如：

```console
azdata arc postgres server edit -n postgres01 -e 'custom_variable_classes = "plpgsql,plperl"'
```

### <a name="pass-an-environment-variable-in-a-parameter-value"></a>在參數值中傳遞環境變數

環境變數應包裝在 "' ' 內，使其在設定之前無法解析。

例如： 

```console
azdata arc postgres server edit -n postgres01 -e 'search_path = "$user"'
```

## <a name="next-steps"></a>後續步驟
- 瞭解如何相應放大 (在您的伺服器群組[) 新增背景工作節點](scale-out-postgresql-hyperscale-server-group.md)
- 深入瞭解向上 [或向下延展 (增加/減少記憶體/虛擬核心) ](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) 伺服器群組

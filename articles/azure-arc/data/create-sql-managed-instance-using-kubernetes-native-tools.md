---
title: 使用 Kubernetes 工具建立 SQL 受控實例
description: 使用 Kubernetes 工具建立 SQL 受控實例
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 2902274cfff11ac256459abd4fe0378146ee067b
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93280176"
---
# <a name="create-azure-sql-managed-instance-using-kubernetes-tools"></a>使用 Kubernetes 工具建立 Azure SQL 受控實例

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Prerequisites

您應該已建立 [Azure Arc 資料控制器](./create-data-controller.md)。

若要使用 Kubernetes 工具建立 SQL 受控實例，您必須安裝 Kubernetes 工具。  本文中的範例將使用 `kubectl` ，但類似的方法可與其他 Kubernetes 工具搭配使用，例如 Kubernetes 儀表板、 `oc` ，或是 `helm` 您熟悉這些工具和 Kubernetes yaml/json。

[安裝 kubectl 工具](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## <a name="overview"></a>總覽

若要建立 SQL 受控實例，您必須建立 Kubernetes 秘密，以安全地儲存系統管理員登入和密碼，以及根據 sqlmanagedinstance 自訂資源定義的 SQL 受控實例自訂資源。

## <a name="create-a-yaml-file"></a>建立 yaml 檔案

您可以使用 [範本 yaml](https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/yaml/sqlmi.yaml) 檔作為起點，以建立您自己的自訂 SQL 受控實例 yaml 檔。  將此檔案下載至您的本機電腦，並在文字編輯器中開啟它。  使用支援語法醒目提示和 linting 的文字編輯器（例如支援 yaml 檔案的語法醒目提示和 [VS Code](https://code.visualstudio.com/download) ）會很有用。

這是範例 yaml 檔：

```yaml
apiVersion: v1
data:
  password: <your base64 encoded password>
  username: <your base64 encoded user name. 'sa' is not allowed>
kind: Secret
metadata:
  name: example-login-secret
type: Opaque
---
apiVersion: sql.arcdata.microsoft.com/v1alpha1
kind: sqlmanagedinstance
metadata:
  name: example
spec:
  limits:
    memory: 4Gi
    vcores: "4"
  requests:
    memory: 2Gi
    vcores: "1"
  service:
    type: LoadBalancer
  storage:
    backups:
      className: default
      size: 5Gi
    data:
      className: default
      size: 5Gi
    datalogs:
      className: default
      size: 5Gi
    logs:
      className: default
      size: 1Gi
```

### <a name="customizing-the-login-and-password"></a>自訂登入和密碼

Kubernetes 秘密會儲存為 base64 編碼的字串，一個用於使用者名稱，另一個用於密碼。  您將需要以 base64 編碼系統管理員登入和密碼，並將它們放在和的預留位置位置中 `data.password` `data.username` 。  請勿包含 `<` `>` 範本中提供的和符號。

> [!NOTE]
> 為了達到最佳安全性，登入不允許使用值 ' sa '。
> 遵循 [密碼複雜性原則](/sql/relational-databases/security/password-policy#password-complexity)。

您可以使用線上工具，以 base64 編碼您想要的使用者名稱和密碼，或根據您的平臺使用內建的 CLI 工具。

PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes('example'))

```

Linux/macOS

```console
echo '<your string to encode here>' | base64

#Example
# echo 'example' | base64
```

### <a name="customizing-the-name"></a>自訂名稱

此範本的名稱屬性值為 ' example '。  您可以變更此名稱，但它必須是遵循 DNS 命名標準的字元。  您也必須變更要符合的密碼名稱。  例如，如果您將 SQL 受控實例的名稱變更為 ' sql1 '，則必須將秘密的名稱從 ' example-login-secret ' 變更為 ' sql1-login-secret '

### <a name="customizing-the-resource-requirements"></a>自訂資源需求

您可以視需要變更資源需求-RAM 和核心限制和要求。  

> [!NOTE]
> 您可以深入瞭解 [Kubernetes 資源管理](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes)。

資源限制和要求的需求：
- **需要** 進行計費的核心限制值。
- 其餘的資源要求和限制都是選擇性的。
- 核心限制和要求必須是正整數值（如果有指定的話）。
- 核心要求至少需要2個核心（如果有指定的話）。
- 記憶體值格式會遵循 Kubernetes 標記法。  
- 如果有指定，記憶體要求最少需要2Gi。
- 一般來說，針對生產環境使用案例，每1個核心應該有4GB 的 RAM。

### <a name="customizing-service-type"></a>自訂服務類型

您可以視需要將服務類型變更為 NodePort。  將指派隨機的埠號碼。

### <a name="customizing-storage"></a>自訂儲存體

您可以自訂儲存體類別的儲存體類別以符合您的環境。  如果您不確定有哪些儲存類別可供使用，您可以執行命令 `kubectl get storageclass` 來加以查看。  範本的預設值為 ' default '。  這表示有一個 _名為_ ' default ' 的儲存類別，但有一個 _預設的儲存_ 類別。  您也可以選擇變更儲存體的大小。  您可以閱讀更多有關 [儲存體](./storage-configuration.md)設定的資訊。

## <a name="creating-the-sql-managed-instance"></a>建立 SQL 受控實例

現在您已自訂 SQL 受控實例 yaml 檔，您可以藉由執行下列命令來建立 SQL 受控實例：

```console
kubectl create -n <your target namespace> -f <path to your yaml file>

#Example
#kubectl create -n arc -f C:\arc-data-services\sqlmi.yaml
```


## <a name="monitoring-the-creation-status"></a>監視建立狀態

建立 SQL 受控實例需要幾分鐘的時間才能完成。 您可以使用下列命令來監視另一個終端機視窗中的進度：

> [!NOTE]
>  下列範例命令假設您已建立名為 ' sql1 ' 的 SQL 受控實例，以及名為 ' arc ' 的 Kubernetes 命名空間。  如果您使用不同的命名空間/SQL 受控實例名稱，您可以將 ' arc ' 和 ' sqlmi ' 取代為您的名稱。

```console
kubectl get sqlmi/sql1 --namespace arc
```

```console
kubectl get pods --namespace arc
```

您也可以執行如下所示的命令，檢查任何特定 pod 的建立狀態。  這特別適用于針對任何問題進行疑難排解。

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/sql1-0 --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>針對建立問題進行疑難排解

如果您在建立麻煩很快找上門時遇到任何問題，請參閱 [疑難排解指南](troubleshoot-guide.md)。

## <a name="next-steps"></a>後續步驟

[連接到 Azure Arc 啟用的 SQL 受控執行個體](connect-managed-instance.md)

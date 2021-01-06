---
title: 使用 Kubernetes 工具建立于 postgresql 超大規模伺服器群組
description: 使用 Kubernetes 工具建立于 postgresql 超大規模伺服器群組
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 30852b6b3f9a4b490c4b58fe07f34ee49c60fa9f
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97955241"
---
# <a name="create-a-postgresql-hyperscale-server-group-using-kubernetes-tools"></a>使用 Kubernetes 工具建立于 postgresql 超大規模伺服器群組

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>先決條件

您應該已建立 [Azure Arc 資料控制器](./create-data-controller.md)。

若要使用 Kubernetes 工具建立于 postgresql 超大規模伺服器群組，您將需要安裝 Kubernetes 工具。  本文中的範例將使用 `kubectl` ，但類似的方法可與其他 Kubernetes 工具搭配使用，例如 Kubernetes 儀表板、 `oc` ，或是 `helm` 您熟悉這些工具和 Kubernetes yaml/json。

[安裝 kubectl 工具](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## <a name="overview"></a>總覽

若要建立于 postgresql 超大規模伺服器群組，您需要建立 Kubernetes 秘密，以安全地儲存您的 postgres 系統管理員登入和密碼，以及以于 postgresql-12 或超大規模-11 自訂資源定義為基礎的于 postgresql 于 postgresql 伺服器群組自訂資源。

## <a name="create-a-yaml-file"></a>建立 yaml 檔案

您可以使用 [範本 yaml](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/postgresql.yaml) 檔作為起點，建立您自己的自訂于 postgresql 超大規模伺服器群組 yaml 檔。  將此檔案下載至您的本機電腦，並在文字編輯器中開啟它。  使用支援語法醒目提示和 linting 的文字編輯器（例如支援 yaml 檔案的語法醒目提示和 [VS Code](https://code.visualstudio.com/download) ）會很有用。

這是範例 yaml 檔：

```yaml
apiVersion: v1
data:
  password: <your base64 encoded password>
kind: Secret
metadata:
  name: example-login-secret
type: Opaque
---
apiVersion: arcdata.microsoft.com/v1alpha1
kind: postgresql-12
metadata:
  generation: 1
  name: example
spec:
  engine:
    extensions:
    - name: citus
  scale:
    shards: 3
  scheduling:
    default:
      resources:
        limits:
          cpu: "4"
          memory: 4Gi
        requests:
          cpu: "1"
          memory: 2Gi
  service:
    type: LoadBalancer
  storage:
    backups:
      className: default
      size: 5Gi
    data:
      className: default
      size: 5Gi
    logs:
      className: default
      size: 1Gi
```

### <a name="customizing-the-login-and-password"></a>自訂登入和密碼。
Kubernetes 秘密會儲存為 base64 編碼的字串，一個用於使用者名稱，另一個用於密碼。  您將需要以 base64 編碼系統管理員登入和密碼，並將它們放在和的預留位置位置中 `data.password` `data.username` 。  請勿包含 `<` `>` 範本中提供的和符號。

您可以使用線上工具，以 base64 編碼您想要的使用者名稱和密碼，或根據您的平臺使用內建的 CLI 工具。

PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('example'))

```

Linux/macOS

```console
echo '<your string to encode here>' | base64

#Example
# echo 'example' | base64
```

### <a name="customizing-the-name"></a>自訂名稱

此範本的名稱屬性值為 ' example '。  您可以變更此名稱，但它必須是遵循 DNS 命名標準的字元。  您也必須變更要符合的密碼名稱。  例如，如果您將于 postgresql 超大規模伺服器群組的名稱變更為 ' postgres1 '，則必須將秘密的名稱從 ' example-login-secret ' 變更為 ' postgres1-login-secret '

### <a name="customizing-the-engine-version"></a>自訂引擎版本

您可以藉由編輯屬性，將引擎版本變更為于 postgresql-11 或于 postgresql-12 `kind` 。

### <a name="customizing-the-resource-requirements"></a>自訂資源需求

您可以視需要變更資源需求-RAM 和核心限制和要求。  

> [!NOTE]
> 您可以深入瞭解 [Kubernetes 資源管理](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes)。

資源限制和要求的需求：
- **需要** 進行計費的核心限制值。
- 其餘的資源要求和限制都是選擇性的。
- 核心限制和要求必須是正整數值（如果有指定的話）。
- 核心要求需要最少1個核心（如果有指定的話）。
- 記憶體值格式會遵循 Kubernetes 標記法。  

### <a name="customizing-service-type"></a>自訂服務類型

您可以視需要將服務類型變更為 NodePort。  將指派隨機的埠號碼。

### <a name="customizing-storage"></a>自訂儲存體

您可以自訂儲存體類別的儲存體類別以符合您的環境。  如果您不確定有哪些儲存類別可供使用，您可以執行命令 `kubectl get storageclass` 來加以查看。  範本的預設值為 ' default '。  這表示有一個 _名為_ ' default ' 的儲存類別，但有一個 _預設的儲存_ 類別。  您也可以選擇變更儲存體的大小。  您可以閱讀更多有關 [儲存體](./storage-configuration.md)設定的資訊。

## <a name="creating-the-postgresql-hyperscale-server-group"></a>建立于 postgresql 超大規模伺服器群組

現在您已自訂于 postgresql 超大規模伺服器群組 yaml 檔案，您可以執行下列命令來建立于 postgresql 超大規模伺服器群組：

```console
kubectl create -n <your target namespace> -f <path to your yaml file>

#Example
#kubectl create -n arc -f C:\arc-data-services\postgres.yaml
```


## <a name="monitoring-the-creation-status"></a>監視建立狀態

建立于 postgresql 超大規模伺服器群組需要幾分鐘的時間才能完成。 您可以使用下列命令來監視另一個終端機視窗中的進度：

> [!NOTE]
>  下列範例命令假設您已建立名為 ' postgres1 ' 的于 postgresql 超大規模伺服器群組和名稱為 ' arc ' 的 Kubernetes 命名空間。  如果您使用不同的命名空間/于 postgresql 超大規模伺服器組名，您可以使用您的名稱取代 ' arc ' 和 ' postgres1 '。

```console
kubectl get postgresql-12/postgres1 --namespace arc
```

```console
kubectl get pods --namespace arc
```

您也可以執行如下所示的命令，檢查任何特定 pod 的建立狀態。  這特別適用于針對任何問題進行疑難排解。

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/postgres1-0 --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>針對建立問題進行疑難排解

如果您在建立麻煩很快找上門時遇到任何問題，請參閱 [疑難排解指南](troubleshoot-guide.md)。
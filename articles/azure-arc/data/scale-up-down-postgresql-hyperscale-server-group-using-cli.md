---
title: '使用 CLI (azdata 或 kubectl 適用於 PostgreSQL 的 Azure 資料庫的超大規模伺服器群組擴大和縮小) '
description: '使用 CLI (azdata 或 kubectl 適用於 PostgreSQL 的 Azure 資料庫的超大規模伺服器群組擴大和縮小) '
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: dc77b3c8bc357b63047d20afa9493bbaaff77113
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91285310"
---
# <a name="scale-up-and-down-an-azure-database-for-postgresql-hyperscale-server-group-using-cli-azdata-or-kubectl"></a>使用 CLI (azdata 或 kubectl 適用於 PostgreSQL 的 Azure 資料庫的超大規模伺服器群組擴大和縮小) 



有時候您可能需要變更伺服器群組的特性或定義。 例如：

- 擴大或縮小每個協調器或背景工作節點使用的虛擬核心數目
- 擴大或縮小每個協調器或背景工作節點使用的記憶體

本指南說明如何調整 vCore 和/或記憶體。

相應增加或減少伺服器群組的 vCore 或記憶體設定，表示您可以設定每個 vCore 和記憶體設定的最小值和/或最大值。 如果您想要將伺服器群組設定為使用特定數量的 vCore 或特定的記憶體數量，您可以將 [最小值] 設定設為等於 [最大值] 設定。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="show-the-current-definition-of-the-server-group"></a>顯示目前的伺服器群組定義

若要顯示目前的伺服器群組定義，並查看目前的 vCore 和記憶體設定，請執行下列其中一個命令：

### <a name="cli-with-azdata"></a>使用 azdata 的 CLI

```console
azdata arc postgres server show -n <server group name>
```
### <a name="cli-with-kubectl"></a>使用 kubectl 的 CLI

```console
kubectl describe postgresql-12/<server group name> [-n <namespace name>]
```
> [!NOTE]
> 如果您已建立于 postgresql 第11版的伺服器群組，請改為執行 `kubectl describe postgresql-11/<server group name>` 。

它會傳回您伺服器群組的設定。 如果您已使用預設設定建立伺服器群組，您應該會看到如下所示的定義：

```console
"scheduling": {
      "default": {
        "resources": {
          "requests": {
            "memory": "256Mi"
          }
        }
      }
    },
```

## <a name="interpret-the-definition-of-the-server-group"></a>解讀伺服器群組的定義

在伺服器群組的定義中，每個節點的最小/最大 vCore 和每個節點的最小/最大記憶體的設定區段都是「 **排程** 」區段。 在該區段中，[最大值] 會保存在稱為「 **限制** 」的子區段中，而最小值設定會保存在稱為「 **要求**」的子區段中。

如果您設定的最小設定與 [最大值] 設定不同，則設定可確保您的伺服器群組會在需要時配置要求的資源。 它不會超過您設定的限制。

您的伺服器群組將實際使用的資源 (虛擬核心和記憶體) ，最高可達最大設定，並取決於叢集上可用的工作負載和資源。 如果您未將設定的上限設定為 [最大值]，您的伺服器群組最多可能會使用 Kubernetes 叢集配置給您的伺服器群組已排程之 Kubernetes 節點的所有資源。

這些 vCore 和記憶體設定會套用至每個于 postgresql 超大規模節點， (協調器節點和背景工作節點) 。 尚未支援個別設定協調器節點和背景工作節點的定義。

在預設設定中，只有最小記憶體會設定為256Mi，因為它是建議執行于 postgresql 超大規模的最小記憶體數量。

> [!NOTE]
> 設定 [最小值] 並不表示伺服器群組一定會使用最小值。 這表示，如果伺服器群組需要，則保證至少會配置此最小值。 例如，讓我們考慮設定 `--minCpu 2` 。 這並不表示伺服器群組隨時都將使用至少2個虛擬核心。 相反地，如果伺服器群組不需要這麼多，就可以開始使用不到2個虛擬核心，如果稍後需要，則會保證配置至少2個虛擬核心。 這表示 Kubernetes 叢集會將資源配置給其他工作負載，以便在需要時將資源配置給伺服器群組。

>[!NOTE]
>在您修改系統的設定之前，請務必先熟悉 Kubernetes [資源模型。](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

## <a name="scale-up-the-server-group"></a>擴大伺服器群組

您即將設定的設定必須在您為 Kubernetes 叢集設定的設定內考慮。 請確定您未設定 Kubernetes 叢集無法滿足的值。 這可能會導致錯誤或無法預期的行為。 例如，如果您的伺服器群組狀態在變更設定之後仍處於 [狀態 _更新_ ]，則可能表示您將下列參數設定為您的 Kubernetes 叢集無法滿足的值。 如果是這種情況，請還原變更或讀取 _troubleshooting_section。

例如，假設您想要將伺服器群組的定義擴大為：

- 最小 vCore = 2
- 最大 vCore = 4
- 最小記憶體 = 512Mb
- 最大記憶體 = 1Gb

您可以使用下列其中一種方法：

### <a name="cli-with-azdata"></a>使用 azdata 的 CLI

```console
azdata arc postgres server edit -n <name of your server group> --cores-request <# core-request>  --cores-limit <# core-limit>  --memory-request <# memory-request>Mi  --memory-limit <# memory-limit>Mi
```

> [!CAUTION]
> 以下範例提供的範例說明如何使用命令。 執行編輯命令之前，請務必將參數設定為 Kubernetes 叢集可接受的值。

```console
azdata arc postgres server edit -n <name of your server group> --cores-request 2  --cores-limit 4  --memory-request 512Mi  --memory-limit 1024Mi
```

此命令會在顯示時成功執行：

```console
<name of your server group> is Ready
```

> [!NOTE]
> 如需這些參數的詳細資訊，請執行 `azdata arc postgres server edit --help` 。

### <a name="cli-with-kubectl"></a>使用 kubectl 的 CLI

```console
kubectl edit postgresql-12/<server group name> [-n <namespace name>]
```

這會帶您進入 vi 編輯器，您可以在其中流覽和變更設定。 使用下列程式，將所需的設定對應至規格中的功能變數名稱：

> [!CAUTION]
> 以下範例提供的範例說明如何編輯設定。 更新設定之前，請務必將參數設定為 Kubernetes 叢集可接受的值。

例如：
- 最小 vCore = 2-> scheduling\default\resources\requests\cpu
- Max vCore = 4-> scheduling\default\resources\limits\cpu
- 最小記憶體 = 512Mb-> scheduling\default\resources\requests\cpu
- 最大記憶體 = 1Gb-> scheduling\default\resources\limits\cpu

如果您不熟悉 vi 編輯器，請參閱 [此處](https://www.computerhope.com/unix/uvi.htm)可能需要的命令描述：
- 編輯模式： `i`
- 使用箭號四處移動
- _stop 編輯： `esc`
- _exit 不儲存： `:qa!`
- 儲存之後 _exit： `:qw!`


## <a name="show-the-scaled-up-definition-of-the-server-group"></a>顯示伺服器群組的相應放大定義

再執行一次命令以顯示伺服器群組的定義，並確認它已設定為您想要的：

### <a name="cli-with-azdata"></a>使用 azdata 的 CLI

```console
azdata arc postgres server show -n <the name of your server group>
```
### <a name="cli-with-kubectl"></a>使用 kubectl 的 CLI

```console
kubectl describe postgresql-12/<server group name>  [-n <namespace name>]
```
> [!NOTE]
> 如果您已建立于 postgresql 第11版的伺服器群組，請改為執行 `kubectl describe postgresql-11/<server group name>` 。


它會顯示伺服器群組的新定義：

```console
"scheduling": {
      "default": {
        "resources": {
          "limits": {
            "cpu": "4",
            "memory": "1024Mi"
          },
          "requests": {
            "cpu": "2",
            "memory": "512Mi"
          }
        }
      }
    },
```

## <a name="scale-down-the-server-group"></a>縮小伺服器群組

若要縮小伺服器群組，您可以執行相同的命令，但針對您想要縮小的設定設定較小的值。 若要移除要求和（或）限制，請將其值指定為空字串。

## <a name="next-steps"></a>後續步驟

- [向外擴充您的適用於 PostgreSQL 的 Azure 資料庫超大規模伺服器群組](scale-out-postgresql-hyperscale-server-group.md)
- [儲存體設定和 Kubernetes 儲存體概念](storage-configuration.md)
- [擴充永久性磁片區宣告](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Kubernetes 資源模型](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

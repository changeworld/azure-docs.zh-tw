---
title: 使用 Azure 傳統 CLI 管理 Azure Cache for Redis
description: 了解如何在任何平台上安裝 Azure 傳統 CLI、如何使用它來連線到您的 Azure 帳戶，以及如何從傳統 CLI 建立及管理「Azure Redis 快取」。
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 01/23/2017
ms.author: yegu
ms.openlocfilehash: c0e94c0bef5087665ca8746a65ccd34a0f61deef
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85829732"
---
# <a name="how-to-create-and-manage-azure-cache-for-redis-using-the-azure-classic-cli"></a>如何使用 Azure 傳統 CLI 來建立及管理 Azure Redis 快取
> [!div class="op_single_selector"]
> * [PowerShell](cache-how-to-manage-redis-cache-powershell.md)
> * [Azure 傳統 CLI](cache-manage-cli.md)
>

Azure 傳統 CLI 是從任何平台管理 Azure 基礎結構的一個好方法。 本文說明如何使用 Azure 傳統 CLI 來建立及管理您的「Azure Redis 快取」執行個體。

[!INCLUDE [outdated-cli-content](../../includes/contains-classic-cli-content.md)]
> [!NOTE]
> 如需最新的 Azure CLI 範例指令碼，請參閱 [Azure CLI Azure Redis 快取範例](cli-samples.md)。

## <a name="prerequisites"></a>必要條件
若要使用 Azure 傳統 CLI 來建立及管理「Azure Redis 快取」執行個體，您必須完成下列步驟。

* 您必須具有 Azure 帳號。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立 [免費帳戶](https://azure.microsoft.com/pricing/free-trial/) 。
* [安裝 Azure 傳統 CLI](../cli-install-nodejs.md)。
* 將您的 Azure CLI 安裝連結到個人 Azure 帳戶，或連結到公司或學校 Azure 帳戶，並使用 `azure login` 命令從傳統 CLI 登入。
* 在執行下列任何命令之前，執行 `azure config mode arm` 命令以將傳統 CLI 切換至資源管理員模式。 如需詳細資訊，請參閱[使用 Azure 傳統 CLI 來管理 Azure 資源與資源群組](../xplat-cli-azure-resource-manager.md)。

## <a name="azure-cache-for-redis-properties"></a>Azure Redis 快取屬性
建立及更新「Azure Redis 快取」執行個體時會使用下列屬性。

| 屬性 | Switch | 描述 |
| --- | --- | --- |
| NAME |-n, --name |「Azure Redis 快取」的名稱。 |
| 資源群組 |-g, --resource-group |資源群組的名稱。 |
| location |-l, --location |要建立快取的位置。 |
| 大小 |-z, --size |「Azure Redis 快取」的大小。 有效的值：[C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4] |
| sku |-x, --sku |Redis SKU。 應為其中一個：[Basic, Standard, Premium] |
| EnableNonSslPort |-e, --enable-non-ssl-port |「Azure Redis 快取」的 EnableNonSslPort 屬性。 如果您想要為您的快取啟用非 TLS/SSL 埠，請新增此旗標 |
| Redis 組態 |-c, --redis-configuration |Redis 組態。 在這裡輸入組態金鑰和值的 JSON 格式字串。 格式："{"":"","":""}" |
| Redis 組態 |-f, --redis-configuration-file |Redis 組態。 在這裡輸入包含組態金鑰和值的檔案路徑。 檔案項目的格式：{"":"","":""} |
| 分區計數 |-r, --shard-count |建立在具有叢集之進階叢集快取的分區數目。 |
| 虛擬網路 |-v, --virtual-network |當快取裝載在 VNET 中時，會指定虛擬網路的確切 ARM 資源識別碼，以將「Azure Redis 快取」部署到其中。 範例格式：/subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| 金鑰類型 |-t, --key-type |要更新的金鑰類型。 有效的值：[Primary, Secondary] |
| StaticIP |-p、--靜態 ip\<static-ip\> |當快取是裝載在 VNET 中，為快取在子網路中指定唯一 IP 位址。 如果未提供，則會從子網路中為您選擇一個。 |
| 子網路 |t, --subnet \<subnet\> |當快取是裝載在 VNET 中，指定要在其中部署快取的子網路。 |
| VirtualNetwork |-v、--虛擬網路\<virtual-network\> |當快取裝載在 VNET 中時，會指定虛擬網路的確切 ARM 資源識別碼，以將「Azure Redis 快取」部署到其中。 範例格式：/subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| 訂用帳戶 |-s, --subscription |訂用帳戶識別碼。 |

## <a name="see-all-azure-cache-for-redis-commands"></a>查看所有 Azure Redis 快取命令
若要查看所有「Azure Redis 快取」命令及其參數，請使用 `azure rediscache -h` 命令。

```azurecli
C:\>azure rediscache -h
help:    Commands to manage your Azure Cache for Redis(s)
help:
help:    Create an Azure Cache for Redis
help:      rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
help:
help:    Delete an existing Azure Cache for Redis
help:      rediscache delete [--name <name> --resource-group <resource-group> ]
help:
help:    List all Azure Cache for Redis within your Subscription or Resource Group
help:      rediscache list [options]
help:
help:    Show properties of an existing Azure Cache for Redis
help:      rediscache show [--name <name> --resource-group <resource-group>]
help:
help:    Change settings of an existing Azure Cache for Redis
help:      rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
help:
help:    Renew the authentication key for an existing Azure Cache for Redis
help:      rediscache renew-key [--name <name> --resource-group <resource-group> ]
help:
help:    Lists Primary and Secondary key of an existing Azure Cache for Redis
help:      rediscache list-keys [--name <name> --resource-group <resource-group>]
help:
help:    Options:
help:      -h, --help  output usage information
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="create-an-azure-cache-for-redis"></a>建立 Azure Cache for Redis
若要建立「Azure Redis 快取」，請使用下列命令：

```azurecli
    azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
```

如需有關此命令的詳細資訊，請執行 `azure rediscache create -h` 命令。

```azurecli
C:\>azure rediscache create -h
help:    Create an Azure Cache for Redis
help:
help:    Usage: rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
help:
help:    Options:
help:      -h, --help                                               output usage information
help:      -v, --verbose                                            use verbose output
help:      -vv                                                      more verbose with debug output
help:      --json                                                   use json output
help:      -n, --name <name>                                        Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>                    Name of the Resource Group
help:      -l, --location <location>                                Location to create cache.
help:      -z, --size <size>                                        Size of the Azure Cache for Redis. Valid values: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4]
help:      -x, --sku <sku>                                          Redis SKU. Should be one of : [Basic, Standard, Premium]
help:      -e, --enable-non-ssl-port                                EnableNonSslPort property of the Azure Cache for Redis. Add this flag if you want to enable the non-TLS/SSL Port for your cache
help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here. Format:"{"<key1>":"<value1>","<key2>":"<value2>"}"
help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here. Format for the file entry: {"<key1>":"<value1>","<key2>":"<value2>"}
help:      -r, --shard-count <shard-count>                          Number of Shards to create on a Premium Cluster Cache
help:      -v, --virtual-network <virtual-network>                  The exact ARM resource ID of the virtual network to deploy the Azure Cache for Redis in. Example format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1
help:      -t, --subnet <subnet>                                    Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network
help:      -p, --static-ip <static-ip>                              Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network
help:      -s, --subscription <id>                                  the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="delete-an-existing-azure-cache-for-redis"></a>刪除現有的 Azure Redis 快取
若要刪除「Azure Redis 快取」，請使用下列命令：

```azurecli
    azure rediscache delete [--name <name> --resource-group <resource-group> ]
```

如需有關此命令的詳細資訊，請執行 `azure rediscache delete -h` 命令。

```azurecli
C:\>azure rediscache delete -h
help:    Delete an existing Azure Cache for Redis
help:
help:    Usage: rediscache delete [--name <name> --resource-group <resource-group> ]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -n, --name <name>                      Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>  Name of the Resource Group under which the cache exists
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="list-all-azure-cache-for-redis-within-your-subscription-or-resource-group"></a>列出您訂用帳戶或資源群組內的所有 Azure Redis 快取
若要列出您「訂用帳戶」或「資源群組」內的所有「Azure Redis 快取」，請使用下列命令：

```azurecli
    azure rediscache list [options]
```

如需有關此命令的詳細資訊，請執行 `azure rediscache list -h` 命令。

```azurecli
C:\>azure rediscache list -h
help:    List all Azure Cache for Redis within your Subscription or Resource Group
help:
help:    Usage: rediscache list [options]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -g, --resource-group <resource-group>  Name of the Resource Group
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="show-properties-of-an-existing-azure-cache-for-redis"></a>顯示現有 Azure Redis 快取的屬性
若要顯示現有「Azure Redis 快取」的屬性，請使用下列命令：

```azurecli
    azure rediscache show [--name <name> --resource-group <resource-group>]
```

如需有關此命令的詳細資訊，請執行 `azure rediscache show -h` 命令。

```azurecli
C:\>azure rediscache show -h
help:    Show properties of an existing Azure Cache for Redis
help:
help:    Usage: rediscache show [--name <name> --resource-group <resource-group>]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -n, --name <name>                      Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>  Name of the Resource Group
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

<a name="scale"></a>

## <a name="change-settings-of-an-existing-azure-cache-for-redis"></a>變更現有 Azure Redis 快取的設定
若要變更現有「Azure Redis 快取」的設定，請使用下列命令：

```azurecli
    azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
```

如需有關此命令的詳細資訊，請執行 `azure rediscache set -h` 命令。

```azurecli
C:\>azure rediscache set -h
help:    Change settings of an existing Azure Cache for Redis
help:
help:    Usage: rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
help:
help:    Options:
help:      -h, --help                                               output usage information
help:      -v, --verbose                                            use verbose output
help:      -vv                                                      more verbose with debug output
help:      --json                                                   use json output
help:      -n, --name <name>                                        Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>                    Name of the Resource Group
help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here.
help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here.
help:      -s, --subscription <subscription>                        the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="renew-the-authentication-key-for-an-existing-azure-cache-for-redis"></a>更新現有 Azure Redis 快取的驗證金鑰
若要更新現有「Azure Redis 快取」的驗證金鑰，請使用下列命令：

```azurecli
    azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]
```

針對 `key-type` 指定 `Primary` 或 `Secondary`。

如需有關此命令的詳細資訊，請執行 `azure rediscache renew-key -h` 命令。

```azurecli
C:\>azure rediscache renew-key -h
help:    Renew the authentication key for an existing Azure Cache for Redis
help:
help:    Usage: rediscache renew-key [--name <name> --resource-group <resource-group> ]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -n, --name <name>                      Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>  Name of the Resource Group under which cache exists
help:      -t, --key-type <key-type>              type of key to renew. Valid values are: 'Primary', 'Secondary'.
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="list-primary-and-secondary-keys-of-an-existing-azure-cache-for-redis"></a>列出現有 Azure Redis 快取的主要和次要金鑰
若要列出現有「Azure Redis 快取」的主要和次要金鑰，請使用下列命令：

```azurecli
    azure rediscache list-keys [--name <name> --resource-group <resource-group>]
```

如需有關此命令的詳細資訊，請執行 `azure rediscache list-keys -h` 命令。

```azurecli
C:\>azure rediscache list-keys -h
help:    Lists Primary and Secondary key of an existing Azure Cache for Redis
help:
help:    Usage: rediscache list-keys [--name <name> --resource-group <resource-group>]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -n, --name <name>                      Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>  Name of the Resource Group under which Cache exists
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

---
title: Azure 連線的機器代理程式 CLI 介面
description: Azure 連接電腦代理 CLI 的參考文檔
author: bobbytreed
manager: carmonm
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
ms.topic: reference
ms.date: 11/04/2019
ms.author: robreed
ms.openlocfilehash: d35c5e283f2e1e2f8afd431d83775167dc2a531a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73513194"
---
# <a name="azure-connected-machine-agent-cli-interface"></a>Azure 連線的機器代理程式 CLI 介面

`Azcmagent` （Azure 連接的電腦代理）工具用於配置和排除與 Azure 連接的非 azure 電腦。

代理本身是在 Linux 上調用`himdsd`的守護進程，以及 Windows 上`himds`調用的 Windows 服務。

在正常使用中，`azcmagent connect`用於在此電腦和 Azure 之間建立連接，`azcmagent disconnect`如果您決定不再需要該連接。 其他命令用於故障排除或其他特殊情況。

## <a name="options"></a>選項。

```none
  -h, --help      help for azcmagent
  -v, --verbose   Increase logging verbosity to show all logs
```

## <a name="see-also"></a>另請參閱

* [阿茲cmagent連接](#azcmagent-connect)- 將此電腦連接到 Azure
* [阿茲cmagent斷開連接](#azcmagent-disconnect)- 斷開此電腦與 Azure 的連接
* [阿茲cmagent重新連接](#azcmagent-reconnect)- 將此電腦重新連接到 Azure
* [阿茲cmagent 顯示](#azcmagent-show)- 獲取電腦中繼資料和代理狀態。 這主要可用於故障排除。
* [阿茲cmagent版本](#azcmagent-version)- 顯示混合管理代理版本

## <a name="azcmagent-connect"></a>阿茲cmagent連接

將此電腦連接到 Azure

### <a name="synopsis"></a>概要

在 Azure 中創建表示此電腦的資源。

這將使用提供的身份驗證選項在 Azure 資源管理器中創建表示此電腦的資源。 資源位於請求的訂閱和資源組中，有關電腦的資料存儲在位置參數指定的 Azure 區域中。
預設資源名稱是此電腦的主機名稱（如果不是重寫）。

然後，將下載與此電腦的系統分配標識對應的證書，並將其存儲在本地。 完成此步驟後 **，Azure 連接的電腦中繼資料**服務和來賓配置代理開始與 Azure 雲同步。

身份驗證選項：

* 訪問權杖`azcmagent connect --access-token <> --subscription-id <> --resource-group <> --location <>`
* 服務主體 ID 和機密`azcmagent connect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid> --subscription-id <> --resource-group <> --location <>`
* 設備登錄（互動式）`azcmagent connect --tenant-id <> --subscription-id <> --resource-group <> --location <>`

### <a name="syntax"></a>語法

```none
azcmagent connect [flags]
```

### <a name="options"></a>選項。

```none
      --access-token string               Access token
  -h, --help                              help for connect
  -l, --location string                   Location of the resource [Required]
      --physical-location string          Physical location of the resource
  -g, --resource-group string             Name of the resource group. [Required]
  -n, --resource-name string              Name of the resource. Defaults to Host Name
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id [Required]
  -t, --tags string                       Tags for resource
      --tenant-id string                  Tenant Id
```

## <a name="azcmagent-disconnect"></a>阿茲cmagent斷開

斷開此電腦與 Azure 的連接

### <a name="synopsis"></a>概要

刪除 Azure 中表示此伺服器的資源。

此命令使用提供的身份驗證選項來刪除表示此電腦的 Azure 資源管理器資源。 在此之後，將斷開**Azure 連接的電腦中繼資料服務和**來賓配置代理。 此命令不會停止或刪除服務：刪除包以便執行此操作。

此命令需要比"Azure 連接的電腦載入"角色更高的許可權。

斷開電腦後，如果要在`azcmagent connect`Azure`azcmagent reconnect`中為其創建新資源，請使用 。

身份驗證選項：

* 訪問權杖`azcmagent disconnect --access-token <>`
* 服務主體 ID 和機密`azcmagent disconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* 互動式設備登錄`azcmagent disconnect --tenant-id <>`

### <a name="syntax"></a>語法

```none
azcmagent disconnect [flags]
```

### <a name="options"></a>選項。

```none
      --access-token string               Access token
  -h, --help                              help for disconnect
  -r, --resource-group string             Name of the resource group
  -n, --resource-name string              Name of the resource
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id
  -t, --tenant-id string                  Tenant Id
```

## <a name="azcmagent-reconnect"></a>阿茲cmagent重新連接

將此電腦重新連接到 Azure

### <a name="synopsis"></a>概要

將憑據不正確電腦重新連接到 Azure。

如果電腦已在 Azure 中具有資源，但無法對其進行身份驗證，則可以使用此命令重新連接它。 如果電腦關閉足夠長的時間使其證書過期（至少 45 天），則可以這樣做。

如果電腦與`azcmagent disconnect`斷開連接，則改`azcmagent connect`用。

此命令使用提供的身份驗證選項來檢索對應于表示此電腦的 Azure 資源管理器資源的新憑據。

此命令需要比**Azure 連接的電腦載入**角色更高的許可權。

驗證選項

* 訪問權杖`azcmagent reconnect --access-token <>`
* 服務主體 ID 和機密`azcmagent reconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* 互動式設備登錄`azcmagent reconnect --tenant-id <>`

### <a name="syntax"></a>語法

```none
azcmagent reconnect [flags]
```

### <a name="options"></a>選項。

```none
      --access-token string               Access token
  -h, --help                              help for reconnect
  -l, --location string                   Location of the resource
  -g, --resource-group string             Name of the resource group.
  -n, --resource-name string              Name of the resource. Defaults to Host Name
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id
      --tenant-id string                  tenant id
```

## <a name="azcmagent-show"></a>阿茲cmagent顯示

獲取電腦中繼資料和代理狀態。 這主要可用於故障排除。

### <a name="synopsis"></a>概要

獲取電腦中繼資料和代理狀態。 這主要可用於故障排除。


### <a name="syntax"></a>語法

```
azcmagent show [flags]
```

### <a name="options"></a>選項。

```
  -h, --help   help for show
```

## <a name="azcmagent-version"></a>阿茲cmagent版本

顯示混合管理代理版本

### <a name="synopsis"></a>概要

顯示混合管理代理版本

### <a name="syntax"></a>語法

```none
azcmagent version [flags]
```

### <a name="options"></a>選項。

```none
  -h, --help   help for version
```

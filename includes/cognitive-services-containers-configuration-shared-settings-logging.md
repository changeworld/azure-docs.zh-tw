---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 873fd8cbc211f098c93b8fb3fbe701e4a34d8487
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "68320530"
---
`Logging` 設定可管理適用於容器的 ASP.NET Core 記錄支援。 對於您用於 ASP.NET Core 應用程式的容器，您可以使用相同的組態設定和值。 

容器支援下列記錄提供者：

|提供者|目的|
|--|--|
|[安慰](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|ASP.NET Core `Console` 記錄提供者。 支援此記錄提供者的所有 ASP.NET Core 組態設定和預設值。|
|[偵錯](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|ASP.NET Core `Debug` 記錄提供者。 支援此記錄提供者的所有 ASP.NET Core 組態設定和預設值。|
|[磁碟](#disk-logging)|JSON 記錄提供者。 此記錄提供者會將記錄資料寫入至輸出裝載。|

此容器命令會以 JSON 格式，將記錄資訊儲存於輸出裝載中：

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
Logging:Disk:Format=json
```

此容器命令會在容器執行時顯示偵錯資訊 (前面加上 `dbug`)：

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
Logging:Console:LogLevel:Default=Debug
```

### <a name="disk-logging"></a>磁碟記錄

`Disk` 記錄提供者支援下列組態設定：

| 名稱 | 資料類型 | 描述 |
|------|-----------|-------------|
| `Format` | String | 適用於記錄檔的輸出格式。<br/> **注意：** 此值必須設定為 `json` 以啟用記錄提供者。 如果在具現化期間指定此值，但沒有同時指定輸出裝載，便會發生錯誤。 |
| `MaxFileSize` | 整數  | 記錄檔的大小上限，以 MB 為單位。 當目前記錄檔的大小符合或超過此值時，記錄提供者便會建立新的記錄檔。 如果指定 -1，記錄檔的大小便只會受限於輸出裝載的檔案大小上限 (若有的話)。 預設值為 1。 |

如需設定 ASP.NET Core 記錄支援的詳細資訊，請參閱[設定檔案組態](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1)。


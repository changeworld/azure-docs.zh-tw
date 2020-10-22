---
title: 將使用方式資料上傳至 Azure 監視器
description: 將使用量 Azure Arc 啟用的資料服務資料上傳至 Azure 監視器
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: 578f0d1ca742fe4445b8aeed6876d1a73fd3f79e
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92375211"
---
# <a name="upload-usage-data-to-azure-monitor"></a>將使用方式資料上傳至 Azure 監視器

您可以定期匯出使用量資訊。 這項資訊的匯出和上傳會在 Azure 中建立及更新資料控制器、SQL 受控實例和于 postgresql 超大規模伺服器群組資源。

> [!NOTE] 
> 在預覽期間，使用 Azure Arc 啟用的資料服務不會產生任何費用。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]


> [!NOTE]
> 在上傳使用方式資料之前，請先等待至少24小時後再建立 Azure Arc 資料控制器。

## <a name="create-service-principal-and-assign-roles"></a>建立服務主體並指派角色

繼續之前，請確定您已建立必要的服務主體，並將它指派給適當的角色。 如需詳細資料，請參閱：
* [建立服務主體](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal)。
* [將角色指派給服務主體](upload-metrics-and-logs-to-azure-monitor.md#assign-roles-to-the-service-principal)

## <a name="upload-usage-data"></a>上傳使用方式資料

您可以透過下列兩個步驟，將使用量資訊（例如清查和資源使用量）上傳至 Azure：

1. 登入資料控制器。 在提示字元中輸入值。 

   ```console
   azdata login
   ```

1. 使用命令匯出使用方式資料 `azdata arc dc export` ，如下所示：

   ```console
   azdata arc dc export --type usage --path usage.json
   ```
 
   此命令會建立一個檔案 `usage.json` ，其中包含所有已啟用 Azure Arc 的資料資源，例如 SQL 受控實例和于 postgresql 超大規模實例等等，這些都是在資料控制器上建立的。

2. 使用命令上傳使用方式資料 ```azdata upload```

   ```console
   azdata arc dc upload --path usage.json
   ```

## <a name="automating-uploads-optional"></a>自動上傳 (選用) 

如果您想要依排程上傳計量和記錄，您可以建立腳本，並每隔幾分鐘在計時器上執行它。 以下是使用 Linux shell 腳本將上傳自動化的範例。

在您最愛的文字/程式碼編輯器中，將下列腳本新增至檔案，並另存為腳本可執行檔，例如 sh (Linux/Mac) 或 .cmd、.bat、ps1。

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

將腳本檔案設為可執行檔

```console
chmod +x myuploadscript.sh
```

每隔20分鐘執行一次腳本：

```console
watch -n 1200 ./myuploadscript.sh
```

您也可以使用諸如 cron 或 Windows 工作排程器的工作排程器，或像是 Ansible、Puppet 或 Chef 的協調器。

## <a name="next-steps"></a>後續步驟

[將計量和記錄上傳至 Azure 監視器](upload-metrics.md)

[將記錄上傳至 Azure 監視器](upload-logs.md)

[將帳單資料上傳至 Azure，並在 Azure 入口網站中加以查看](view-billing-data-in-azure.md)

[在 Azure 入口網站中查看 Azure Arc 的資料控制器資源](view-data-controller-in-azure-portal.md)

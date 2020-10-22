---
title: 將記錄上傳至 Azure 監視器
description: 將 Azure Arc 啟用的資料服務的記錄上傳至 Azure 監視器
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: f3f29ae1ab868a96e6f70ed964f79c47bc591c4d
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92375218"
---
# <a name="upload-logs-to-azure-monitor"></a>將記錄上傳至 Azure 監視器

您可以定期匯出記錄，然後將它們上傳至 Azure。 匯出和上傳記錄檔也會在 Azure 中建立及更新資料控制器、SQL 受控實例和于 postgresql 超大規模伺服器群組資源。

> [!NOTE] 
> 在預覽期間，使用 Azure Arc 啟用的資料服務不會產生任何費用。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="before-you-begin"></a>開始之前

在您可以上傳記錄之前，您需要： 

1. [建立 log analytics 工作區](#create-a-log-analytics-workspace)
1. [將識別碼和共用金鑰指派給環境變數](#assign-id-and-shared-key-to-environment-variables)

## <a name="create-a-log-analytics-workspace"></a>建立 log analytics 工作區

若要建立 log analytics 工作區，請執行下列命令來建立 Log Analytics 工作區，並將存取訊號設定為環境變數。

> [!NOTE]
> 如果您已經有工作區，請略過此步驟。

```azurecli
az monitor log-analytics workspace create --resource-group <resource group name> --workspace-name <some name you choose>
```

範例輸出︰

```output
{
  "customerId": "d6abb435-2626-4df1-b887-445fe44a4123",
  "eTag": null,
  "id": "/subscriptions/<Subscription ID>/resourcegroups/user-arc-demo/providers/microsoft.operationalinsights/workspaces/user-logworkspace",
  "location": "eastus",
  "name": "user-logworkspace",
  "portalUrl": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "user-arc-demo",
  "retentionInDays": 30,
  "sku": {
    "lastSkuUpdate": "Thu, 30 Jul 2020 22:37:53 GMT",
    "maxCapacityReservationLevel": 3000,
    "name": "pergb2018"
  },
  "source": "Azure",
  "tags": null,
  "type": "Microsoft.OperationalInsights/workspaces"
}
```

## <a name="assign-id-and-shared-key-to-environment-variables"></a>將識別碼和共用金鑰指派給環境變數

將記錄工作區分析儲存 `customerId` 為環境變數，以供稍後使用：

::: zone pivot="client-operating-system-windows-command"

```console
SET WORKSPACE_ID=<customerId>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:WORKSPACE_ID='<customerId>'
```
::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export WORKSPACE_ID='<customerId>'
```
::: zone-end

此命令會傳回連接至 log analytics 工作區所需的存取金鑰：

```azurecli
az monitor log-analytics workspace get-shared-keys --resource-group MyResourceGroup --workspace-name MyLogsWorkpace
```

範例輸出︰

```output
{
  "primarySharedKey": "JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123==",
  "secondarySharedKey": "p2XHSxLJ4o9IAqm2zINcEmx0UWU5Z5EZz8PQC0OHpFjdpuVaI0zsPbTv5VyPFgaCUlCZb2yEbkiR4eTuTSF123=="
}
```

將主要金鑰儲存在環境變數中，以供稍後使用：

::: zone pivot="client-operating-system-windows-command"

```console
SET WORKSPACE_SHARED_KEY=<primarySharedKey>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:WORKSPACE_SHARED_KEY='<primarySharedKey>'
```
```
::: zone-end


::: zone pivot="client-operating-system-macos-and-linux"

```console
export WORKSPACE_SHARED_KEY='<primarySharedKey>'
```

::: zone-end

## <a name="set-final-environment-variables-and-confirm"></a>設定最終環境變數並確認

在環境變數中設定 SPN 授權 URL：

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_AUTHORITY=https://login.microsoftonline.com
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end


## <a name="verify-environment-variables"></a>確認環境變數

如果您想要的話，請檢查並確定已設定所有必要的環境變數：


::: zone pivot="client-operating-system-windows-command"

```console
echo %WORKSPACE_ID%
echo %WORKSPACE_SHARED_KEY%
echo %SPN_TENANT_ID%
echo %SPN_CLIENT_ID%
echo %SPN_CLIENT_SECRET%
echo %SPN_AUTHORITY%
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:WORKSPACE_ID
$Env:WORKSPACE_SHARED_KEY
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY
```


::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
echo $WORKSPACE_ID
echo $WORKSPACE_SHARED_KEY
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

::: zone-end

設定環境變數之後，您就可以將記錄上傳至記錄工作區。 

## <a name="upload-logs-to-azure-monitor"></a>將記錄上傳至 Azure 監視器

 若要為 Azure Arc 啟用的 SQL 受控實例和啟用 AzureArc 的于 postgresql 超大規模伺服器群組上傳記錄，請執行下列 CLI 命令-

1. 使用登入 Azure Arc 資料控制器 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 。

   ```console
   azdata login
   ```

   遵循提示來設定命名空間、系統管理員使用者名稱和密碼。 

1. 將所有記錄匯出到指定的檔案：

   ```console
   azdata arc dc export --type logs --path logs.json
   ```

2. 將記錄上傳至 Azure 監視器 log analytics 工作區：

   ```console
   azdata arc dc upload --path logs.json
   ```

## <a name="view-your-logs-in-azure-portal"></a>在 Azure 入口網站中查看您的記錄

上傳記錄之後，您應該就能夠使用記錄查詢總管來進行查詢，如下所示：

1. 開啟 Azure 入口網站，然後在頂端搜尋列中依名稱搜尋您的工作區，然後選取它。
2. 在左面板中選取 [記錄]。
3. 選取開始 (或選取 [消費者入門] 頁面上的連結，以深入瞭解 Log Analytics，如果您不熟悉它) 。
4. 如果這是您第一次使用 Log Analytics，請遵循本教學課程來深入瞭解 Log Analytics。
5. 展開資料表清單底部的 [自訂記錄]，您會看到名為 'sql_instance_logs_CL' 的資料表。
6. 選取資料表名稱旁邊的「眼睛」圖示。
7. 選取 [在查詢編輯器中查看] 按鈕。
8. 您現在可以在查詢編輯器中使用查詢，在記錄檔中顯示最近的10個事件。
9. 從這裡開始，您可以使用查詢編輯器來實驗記錄查詢和設定警示等等。

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

[將使用方式資料、計量和記錄上傳至 Azure 監視器](upload-usage-data.md)

[將帳單資料上傳至 Azure，並在 Azure 入口網站中加以查看](view-billing-data-in-azure.md)

[在 Azure 入口網站中查看 Azure Arc 的資料控制器資源](view-data-controller-in-azure-portal.md)

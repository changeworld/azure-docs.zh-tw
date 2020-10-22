---
title: 將計量上傳至 Azure 監視器
description: 將 Azure Arc 啟用的資料服務計量上傳至 Azure 監視器
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: f31cf22ae8814975dcee92c33026d223275cf121
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92375214"
---
# <a name="upload-metrics-to-azure-monitor"></a>將計量上傳至 Azure 監視器

您可以定期匯出監視計量，然後將其上傳至 Azure。 匯出和上傳資料也會在 Azure 中建立及更新資料控制器、SQL 受控實例和于 postgresql 超大規模伺服器群組資源。

> [!NOTE] 
> 在預覽期間，使用 Azure Arc 啟用的資料服務不會產生任何費用。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>先決條件

繼續之前，請確定您已建立必要的服務主體，並將它指派給適當的角色。 如需詳細資料，請參閱：
* [建立服務主體](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal)。
* [將角色指派給服務主體](upload-metrics-and-logs-to-azure-monitor.md#assign-roles-to-the-service-principal)

## <a name="upload-metrics"></a>上傳計量

使用 Azure Arc 資料服務時，您可以選擇性地將您的計量上傳至 Azure 監視器，以便匯總及分析計量、引發警示、傳送通知，或觸發自動化動作。 

將您的資料傳送至 Azure 監視器也可讓您將計量資料儲存在異地且大規模地儲存，讓資料長期儲存以進行 advanced analytics。

如果您有多個網站有 Azure Arc 的資料服務，您可以使用 Azure 監視器作為集中位置，以收集整個網站的所有記錄和計量。

## <a name="set-final-environment-variables-and-confirm"></a>設定最終環境變數並確認

在環境變數中設定 SPN 授權 URL：

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

如果您想要的話，請檢查並確定已設定所有必要的環境變數：


::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY
```


::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

::: zone-end

::: zone pivot="client-operating-system-windows-command"

```console
echo %SPN_TENANT_ID%
echo %SPN_CLIENT_ID%
echo %SPN_CLIENT_SECRET%
echo %SPN_AUTHORITY%
```

::: zone-end

## <a name="upload-metrics-to-azure-monitor"></a>將計量上傳至 Azure 監視器

若要為已啟用 Azure arc 的 SQL 受控實例上傳計量，並 Azure Arc 啟用的于 postgresql 超大規模伺服器群組執行，請執行下列 CLI 命令：

1. 使用登入資料控制器 `azdata` 。
 
1. 將所有計量匯出至指定的檔案：

   ```console
   azdata arc dc export --type metrics --path metrics.json
   ```

2. 將計量上傳至 Azure 監視器：

   ```console
   azdata arc dc upload --path metrics.json
   ```

   >[!NOTE]
   >為第一次上傳建立 Azure Arc 啟用的資料實例之後，請至少等候30分鐘。
   >
   >在 `upload` `export` Azure 監視器只接受過去30分鐘的計量後，請確定計量立即。 [深入了解](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting)。


如果您在匯出期間看到任何表示「無法取得計量」的錯誤，請執行下列命令來檢查是否已將資料收集設定為 `true` ：

```console
azdata arc dc config show
```

在 [安全性] 區段下查看

```output
 "security": {
      "allowDumps": true,
      "allowNodeMetricsCollection": true,
      "allowPodMetricsCollection": true,
      "allowRunAsRoot": false
    },
```

確認 `allowNodeMetricsCollection` 和 `allowPodMetricsCollection` 屬性是否設定為 `true` 。

## <a name="view-the-metrics-in-the-portal"></a>在入口網站中檢視計量

上傳您的計量之後，您可以從 Azure 入口網站加以查看。
> [!NOTE]
> 請注意，上傳的資料可能需要幾分鐘的時間才會被處理，然後您才能在入口網站中查看計量。


若要在入口網站中查看您的計量，請使用此連結來開啟入口網站： <https://portal.azure.com> 然後在搜尋列中依名稱搜尋您的資料庫實例：

您可以在 [總覽] 頁面上查看 CPU 使用率，或者，如果您想要更詳細的計量，您可以按一下左側導覽面板中的計量

選擇 sql server 作為度量命名空間：

選取您要視覺化的度量 (您也可以選取多個) ：

將頻率變更為過去30分鐘：

> [!NOTE]
> 您只可上傳過去30分鐘的計量。 Azure 監視器會拒絕超過30分鐘的計量。

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

## <a name="general-guidance-on-exporting-and-uploading-usage-metrics"></a>匯出和上傳使用方式、計量的一般指引

在 Azure Arc 啟用的資料服務上建立、讀取、更新和刪除 (CRUD) 作業，以供計費和監視之用。 有背景服務會監視這些 CRUD 作業，並適當地計算耗用量。 使用量或耗用量的實際計算會依排程進行，並在背景中完成。 

在預覽期間，此程式會在夜間進行。 一般的指導方針是每天上傳一次使用。 當使用資訊在同一個24小時期間內匯出和上傳多次時，只會在 Azure 入口網站中更新資源清查，但不會更新資源使用量。

針對上傳計量，Azure 監視器只會接受過去30分鐘的資料， ([深入瞭解](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting)) 。 上傳計量的指引是在建立匯出檔案之後，立即上傳計量，讓您可以在 Azure 入口網站中查看整個資料集。 比方說，如果您在下午2:00 匯出計量，並在下午2:50 執行上傳命令。 由於 Azure 監視器只接受過去30分鐘的資料，因此您可能不會在入口網站中看到任何資料。 

## <a name="next-steps"></a>後續步驟

[將記錄上傳至 Azure 監視器](upload-logs.md)

[將使用方式資料、計量和記錄上傳至 Azure 監視器](upload-usage-data.md)

[將帳單資料上傳至 Azure，並在 Azure 入口網站中加以查看](view-billing-data-in-azure.md)

[在 Azure 入口網站中查看 Azure Arc 的資料控制器資源](view-data-controller-in-azure-portal.md)

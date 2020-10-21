---
title: 將使用方式資料、計量和記錄上傳至 Azure 監視器
description: 將資源清查、使用量資料、計量和記錄上傳至 Azure 監視器
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 869bfcb87aa4846674db233c4268e9269929cd04
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92320172"
---
# <a name="upload-usage-data-metrics-and-logs-to-azure-monitor"></a>將使用方式資料、計量和記錄上傳至 Azure 監視器

您可以定期匯出使用量資訊，以便進行計費、監視計量和記錄，然後將其上傳至 Azure。  這三種資料類型的匯出和上傳也會在 Azure 中建立及更新資料控制器、SQL 受控實例和于 postgresql 超大規模伺服器群組資源。

> [!NOTE] 
> 在預覽期間，使用 Azure Arc 啟用的資料服務不會產生任何費用。

## <a name="prerequisites"></a>先決條件

您將需要 Azure CLI (az) 和 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 已安裝。  [安裝工具](./install-client-tools.md)。

將資料上傳至 Azure 之前，您必須確定您的 Azure 訂用帳戶已註冊 Microsoft AzureData 資源提供者。

您可以藉由執行下列命令來確認這一點：

```console
az provider show -n Microsoft.AzureData -o table
```

如果您的訂用帳戶中目前沒有註冊資源提供者，您可以執行下列命令來註冊該資源提供者。  此命令需要一或兩分鐘的時間才能完成。

```console
az provider register -n Microsoft.AzureData --wait
```

## <a name="upload-usage-data"></a>上傳使用方式資料

您可以透過下列兩個步驟，將使用量資訊（例如清查和資源使用量）上傳至 Azure：

1. 使用命令匯出使用方式資料 `azdata export` ，如下所示：

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #run the export command
   azdata arc dc export --type usage --path usage.json
   ```
   此命令會建立一個檔案 `usage.json` ，其中包含所有已啟用 Azure Arc 的資料資源，例如 SQL 受控實例和于 postgresql 超大規模實例等等，這些都是在資料控制器上建立的。

2. 使用命令上傳使用方式資料 `azdata upload`

   > [!NOTE]
   > 在執行上傳之前，請先等待至少24小時後再建立 Azure Arc 資料控制器

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #run the upload command
   azdata arc dc upload --path usage.json
   ```

## <a name="upload-metrics-and-logs"></a>上傳計量和記錄

使用 Azure Arc 資料服務時，您可以選擇性地將計量和記錄上傳至 Azure 監視器，以便匯總及分析計量、記錄、引發警示、傳送通知，或觸發自動化動作。 

將您的資料傳送至 Azure 監視器也可讓您儲存大規模的監視和記錄資料，並以大規模的大規模儲存資料，讓資料進行長期儲存以進行 advanced analytics。

如果您有多個網站有 Azure Arc 的資料服務，您可以使用 Azure 監視器作為集中位置，以收集整個網站的所有記錄和計量。

### <a name="before-you-begin"></a>開始之前

若要啟用記錄和計量上傳案例，需要進行幾個一次性的設定步驟：

1. 建立服務主體/Azure Active Directory 應用程式，包括建立用戶端存取秘密，並將服務主體指派給您的資料庫實例資源所在的訂用帳戶 () s 上的「監視計量發行者」角色。
2. 建立 log analytics 工作區並取得金鑰，並在環境變數中設定資訊。

第一個專案是上傳計量所需的專案，第二個則是上傳記錄所需的專案。

遵循這些命令來建立您的計量上傳服務主體，並將其指派給「監視計量發行者」和「參與者」角色，讓服務主體可以上傳計量並執行建立和上傳作業。

## <a name="create-service-principal-and-assign-roles"></a>建立服務主體並指派角色

請遵循下列命令來建立您的計量上傳服務主體，並將其指派給「監視計量發行者」角色：

若要建立服務主體，請執行下列命令：

> [!NOTE]
> 建立服務主體需要 [Azure 中的特定許可權](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)。

```console
az ad sp create-for-rbac --name <a name you choose>

#Example:
#az ad sp create-for-rbac --name azure-arc-metrics
```

範例輸出︰

```output
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

將 appId 和租使用者值儲存在環境變數中，以供稍後使用。 

若要使用 PowerShell 來儲存 appId 和租使用者值，請遵循此範例：

```powershell
$Env:SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'
```

或者，在 Linux 或 macOS 上，您可以使用下列範例來儲存 appId 和 tenant 值：

   ```console
   export SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
   export SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
   export SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'

   #Example (using Linux):
   export SPN_CLIENT_ID='2e72adbf-de57-4c25-b90d-2f73f126e123'
   export SPN_CLIENT_SECRET='5039d676-23f9-416c-9534-3bd6afc78123'
   export SPN_TENANT_ID='72f988bf-85f1-41af-91ab-2d7cd01ad1234'
   ```

執行此命令，將服務主體指派給您資料庫實例資源所在之訂用帳戶上的「監視計量發行者」角色：


> [!NOTE]
> 在 Windows 環境中執行時，您必須使用雙引號作為角色名稱。


```console
az role assignment create --assignee <appId value from output above> --role "Monitoring Metrics Publisher" --scope subscriptions/<sub ID>
az role assignment create --assignee <appId value from output above> --role 'Contributor' --scope subscriptions/<sub ID>

#Example:
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role "Monitoring Metrics Publisher" --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role 'Contributor' --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123

#On Windows environment
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role "Monitoring Metrics Publisher" --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role "Contributor" --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
```

範例輸出︰

```console
{
  "canDelegate": null,
  "id": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/<Subscription ID>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="create-a-log-analytics-workspace"></a>建立 log analytics 工作區

接下來，執行下列命令以建立 Log Analytics 工作區，並將存取訊號設定為環境變數。

> [!NOTE]
> 如果您已經有工作區，請略過此步驟。

```console
az monitor log-analytics workspace create --resource-group <resource group name> --workspace-name <some name you choose>

#Example:
#az monitor log-analytics workspace create --resource-group MyResourceGroup --workspace-name MyLogsWorkpace
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

將 customerId (工作區識別碼) 儲存為環境變數，以供稍後使用：

```console
#PowerShell
$Env:WORKSPACE_ID='<the customerId from the 'log-analytics workspace create' command output above>'

#Linux/macOS
export WORKSPACE_ID='<the customerId from the 'log-analytics workspace create' command output above>'

#Example (using Linux)
#export WORKSPACE_ID='d6abb435-2626-4df1-b887-445fe44a4123'
```

此命令會列印連接至 log analytics 工作區所需的存取金鑰：

```console
az monitor log-analytics workspace get-shared-keys --resource-group MyResourceGroup --workspace-name MyLogsWorkpace
```

範例輸出︰

```console
{
  "primarySharedKey": "JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123==",
  "secondarySharedKey": "p2XHSxLJ4o9IAqm2zINcEmx0UWU5Z5EZz8PQC0OHpFjdpuVaI0zsPbTv5VyPFgaCUlCZb2yEbkiR4eTuTSF123=="
}
```

將主要金鑰儲存在環境變數中，以供稍後使用：

```console
#PowerShell:
$Env:WORKSPACE_SHARED_KEY='<the primarySharedKey value from the 'get-shared-keys' command above'

#Linux/macOS:
export WORKSPACE_SHARED_KEY='<the primarySharedKey value from the 'get-shared-keys' command above'

#Example (using Linux):
export WORKSPACE_SHARED_KEY='JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123=='

```

## <a name="set-final-environment-variables-and-confirm"></a>設定最終環境變數並確認

在環境變數中設定 SPN 授權 URL：

```console
#PowerShell
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'

#Linux/macOS:
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

如果您想要的話，請檢查並確定已設定所有必要的環境變數：

```console
#PowerShell
$Env:WORKSPACE_ID
$Env:WORKSPACE_SHARED_KEY
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY

#Linux/macOS
echo $WORKSPACE_ID
echo $WORKSPACE_SHARED_KEY
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

## <a name="upload-metrics-to-azure-monitor"></a>將計量上傳至 Azure 監視器

若要為已啟用 Azure arc 的 SQL 受控實例上傳計量，並 Azure Arc 啟用的于 postgresql 超大規模伺服器群組執行，請執行下列 CLI 命令：

1. 將所有計量匯出至指定的檔案：

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #export the metrics
   azdata arc dc export --type metrics --path metrics.json
   ```

2. 將計量上傳至 Azure 監視器：

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #upload the metrics
   azdata arc dc upload --path metrics.json
   ```

   >[!NOTE]
   >為第一次上傳建立 Azure Arc 啟用的資料實例之後，至少等候30分鐘
   >
   >在 `upload` `export` Azure 監視器只接受過去30分鐘的計量後，請確定計量立即。 [深入了解](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting)


如果您在匯出期間看到任何表示「無法取得計量」的錯誤，請執行下列命令來檢查是否已將資料收集設定為 ```true``` ：

```console
azdata arc dc config show
```

並查看「安全性區段」

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

## <a name="upload-logs-to-azure-monitor"></a>將記錄上傳至 Azure 監視器

 若要為 Azure Arc 啟用的 SQL 受控實例和啟用 AzureArc 的于 postgresql 超大規模伺服器群組上傳記錄，請執行下列 CLI 命令-

1. 將所有記錄匯出到指定的檔案：

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #export the logs
   azdata arc dc export --type logs --path logs.json
   ```

2. 將記錄上傳至 Azure 監視器 log analytics 工作區：

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #Upload the logs
   azdata arc dc upload --path logs.json
   ```

## <a name="view-your-logs-in-azure-portal"></a>在 Azure 入口網站中查看您的記錄

上傳記錄之後，您應該就能夠使用記錄查詢總管來進行查詢，如下所示：

1. 開啟 Azure 入口網站，然後在頂端搜尋列中依名稱搜尋您的工作區，然後選取它
2. 按一下左面側板中的 [記錄]
3. 按一下 [開始 (或按一下 [消費者入門] 頁面上的連結，以深入瞭解 Log Analytics，如果您不熟悉它) 
4. 如果這是您第一次使用 Log Analytics，請遵循本教學課程來深入瞭解 Log Analytics
5. 展開資料表清單底部的 [自訂記錄]，您會看到名為 'sql_instance_logs_CL' 的資料表。
6. 按一下資料表名稱旁的 [眼睛] 圖示
7. 按一下 [在查詢編輯器中檢視] 按鈕
8. 您現在可以在查詢編輯器中使用查詢，在記錄檔中顯示最新的10個事件
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

## <a name="general-guidance-on-exporting-and-uploading-usage-metrics"></a>匯出和上傳使用方式、計量的一般指引

在 Azure Arc 啟用的資料服務上建立、讀取、更新和刪除 (CRUD) 作業，以供計費和監視之用。 有背景服務會監視這些 CRUD 作業，並適當地計算耗用量。 使用量或耗用量的實際計算會依排程進行，並在背景中完成。 

在預覽期間，此程式會在夜間進行。 一般的指導方針是每天上傳一次使用。 當使用資訊在同一個24小時期間內匯出和上傳多次時，只會在 Azure 入口網站中更新資源清查，但不會更新資源使用量。

針對上傳計量，Azure 監視器只會接受過去30分鐘的資料， ([深入瞭解](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting)) 。 上傳計量的指引是在建立匯出檔案之後，立即上傳計量，讓您可以在 Azure 入口網站中查看整個資料集。 比方說，如果您在下午2:00 匯出計量，並在下午2:50 執行上傳命令。 由於 Azure 監視器只接受過去30分鐘的資料，因此您可能不會在入口網站中看到任何資料。 

## <a name="next-steps"></a>後續步驟

[將帳單資料上傳至 Azure，並在 Azure 入口網站中加以查看](view-billing-data-in-azure.md)

[在 Azure 入口網站中查看 Azure Arc 的資料控制器資源](view-data-controller-in-azure-portal.md)
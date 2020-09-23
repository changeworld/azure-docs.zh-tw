---
title: 將資源清查、使用量資料、計量和記錄上傳至 Azure 監視器
description: 將資源清查、使用量資料、計量和記錄上傳至 Azure 監視器
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: ac6ffd2b5bf48079db6a0cd261dbe2535e1821ac
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934318"
---
# <a name="upload-resource-inventory-usage-data-metrics-and-logs-to-azure-monitor"></a>將資源清查、使用量資料、計量和記錄上傳至 Azure 監視器

使用 Azure Arc 資料服務時，您可以 *選擇性地* 將計量和記錄上傳至 Azure 監視器，以便匯總及分析計量、記錄、引發警示、傳送通知或觸發自動化動作。 將您的資料傳送至 Azure 監視器也可讓您將監視和記錄資料從網站和大規模儲存，以長期儲存資料以進行先進的分析。  如果您有多個網站有 Azure Arc 的資料服務，您可以使用 Azure 監視器作為集中位置，以收集整個網站的所有記錄和計量。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="before-you-begin"></a>開始之前

若要啟用記錄和計量上傳案例，需要進行幾個一次性的設定步驟：

1) 建立服務主體/Azure Active Directory 應用程式，包括建立用戶端存取秘密，並將服務主體指派給您的資料庫實例資源所在的訂用帳戶 () s 上的「監視計量發行者」角色。
2) 建立 log analytics 工作區並取得金鑰，並在環境變數中設定資訊。

第一個專案是上傳計量所需的專案，第二個則是上傳記錄所需的專案。

遵循這些命令來建立您的計量上傳服務主體，並將其指派給「監視計量發行者」和「參與者」角色，讓服務主體可以上傳計量並執行建立和上傳作業。

## <a name="create-service-principal-and-assign-roles"></a>建立服務主體並指派角色

請遵循下列命令來建立您的計量上傳服務主體，並將其指派給「監視計量發行者」角色：

若要建立服務主體，請執行下列命令：

> [!NOTE]
> 建立服務主體需要 [Azure 中的特定許可權](/active-directory/develop/howto-create-service-principal-portal#required-permissions)。

```console
az ad sp create-for-rbac --name <a name you choose>

#Example:
#az ad sp create-for-rbac --name azure-arc-metrics
```

範例輸出︰

```console
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

將 appId 和租使用者值儲存在環境變數中，以供稍後使用：

```console
#PowerShell

$Env:SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'

#Linux/macOS

export SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
export SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
export SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'

#Example (using Linux):
export SPN_CLIENT_ID='2e72adbf-de57-4c25-b90d-2f73f126e123'
export SPN_CLIENT_SECRET='5039d676-23f9-416c-9534-3bd6afc78123'
export SPN_TENANT_ID='72f988bf-85f1-41af-91ab-2d7cd01ad1234'
```

執行此命令，將服務主體指派給您資料庫實例資源所在之訂用帳戶上的「監視計量發行者」角色：

```console
az role assignment create --assignee <appId value from output above> --role 'Monitoring Metrics Publisher' --scope subscriptions/<sub ID>
az role assignment create --assignee <appId value from output above> --role 'Contributor' --scope subscriptions/<sub ID>

#Example:
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role 'Monitoring Metrics Publisher' --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role 'Contributor' --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
```

範例輸出︰

```console
{
  "canDelegate": null,
  "id": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="create-a-log-analytics-workspace"></a>建立 log analytics 工作區

接下來，執行下列命令以建立 Log Analytics 工作區，並將存取訊號設定為環境變數。

> [!NOTE]
> 如果您已經有工作區，請略過此步驟。

```console
az monitor log-analytics workspace create --resource-group <resource group name> --name <some name you choose>

#Example:
#az monitor log-analytics workspace create --resource-group MyResourceGroup --name MyLogsWorkpace
```

範例輸出︰

```console
{
  "customerId": "d6abb435-2626-4df1-b887-445fe44a4123",
  "eTag": null,
  "id": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123/resourcegroups/user-arc-demo/providers/microsoft.operationalinsights/workspaces/user-logworkspace",
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
az monitor log-analytics workspace get-shared-keys --resource-group MyResourceGroup --name MyLogsWorkpace
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

若要為您的 Azure SQL 受控實例和適用於 PostgreSQL 的 Azure 資料庫超大規模伺服器群組上傳計量，請執行下列 CLI 命令：

這會將所有計量匯出至指定檔案：

```console
azdata arc dc export -t metrics --path metrics.json
```

這會將計量上傳至 Azure 監視器：

```console
azdata arc dc upload --path metrics.json
```

## <a name="view-the-metrics-in-the-portal"></a>在入口網站中檢視計量

上傳計量之後，您應該能夠從 Azure 入口網站將其視覺化。

若要在入口網站中查看您的計量，請使用此特殊連結來開啟入口網站： <https://portal.azure.com> 然後在搜尋列中依名稱搜尋您的資料庫實例：

您可以在 [總覽] 頁面上查看 CPU 使用率，或者，如果您想要更詳細的計量，您可以按一下左側導覽面板中的計量

選擇 sql server 作為度量命名空間：

選取您要視覺化的度量 (您也可以選取多個) ：

將頻率變更為過去30分鐘：

> [!NOTE]
> 您只可上傳過去30分鐘的計量。 Azure 監視器會拒絕超過30分鐘的計量。

## <a name="upload-logs-to-azure-monitor"></a>將記錄上傳至 Azure 監視器

 若要上傳 Azure SQL 受控實例和適用於 PostgreSQL 的 Azure 資料庫超大規模伺服器群組的記錄，請執行下列 CLI 命令-

這會將所有記錄匯出到指定檔案：

```console
azdata arc dc export -t logs --path logs.json
```

這會將記錄上傳至 Azure 監視器 log analytics 工作區：

```console
azdata arc dc upload --path logs.json
```

## <a name="view-your-logs-in-azure-portal"></a>在 Azure 入口網站中查看您的記錄

上傳記錄之後，您應該就能夠使用記錄查詢總管來進行查詢，如下所示：

1. 開啟 Azure 入口網站，然後在頂端搜尋列中依名稱搜尋您的工作區，然後選取它
2. 按一下左面側板中的 [記錄]
3. 按一下 [開始 (或按一下 [消費者入門] 頁面上的連結，以深入瞭解 Log Analytics，如果您不熟悉它) 
4. 如果是您的第一次，請遵循本教學課程來深入瞭解 Log Analytics
5. 展開資料表清單底部的 [自訂記錄]，您會看到名為 'sql_instance_logs_CL' 的資料表。
6. 按一下資料表名稱旁的 [眼睛] 圖示
7. 按一下 [在查詢編輯器中檢視] 按鈕
8. 您現在在 [查詢編輯器] 中有一個查詢，這會在記錄中顯示最新的 10 個事件
9. 從這裡開始，您可以使用查詢編輯器來實驗記錄查詢和設定警示等等。

## <a name="automating-metrics-and-logs-uploads-optional"></a>將計量和記錄上傳自動化 (選擇性) 

如果您想要持續上傳計量和記錄，您可以建立腳本，並每隔幾分鐘在計時器上執行它。  以下是使用 Linux shell 腳本將上傳自動化的範例。

在您最愛的文字/程式碼編輯器中，將下列內容新增至檔案，並儲存為腳本可執行檔，例如 (Linux/Mac) 或 .cmd、.bat、ps1。

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

將腳本檔案設為可執行檔

```console
chmod +x myuploadscript.sh
```

每2分鐘執行一次腳本：

```console
watch -n 120 ./myuploadscript.sh
```

您也可以使用諸如 cron 或 Windows 工作排程器的工作排程器，或像是 Ansible、Puppet 或 Chef 的協調器。

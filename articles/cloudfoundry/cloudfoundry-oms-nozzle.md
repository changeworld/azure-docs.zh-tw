---
title: 部署 Azure Log Analytics 噴嘴以進行 Cloud Foundry 監視
description: 部署 Azure Log Analytics 之 Cloud Foundry Loggregator Nozzle 的逐步指引。 您可以使用 Nozzle 來監視 Cloud Foundry 系統健全狀況和效能計量。
services: virtual-machines-linux
author: ningk
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: ningk
ms.openlocfilehash: fde0afcd37cd464b0b87e5ccd257d4a7a684eeb0
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93040767"
---
# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>部署適用於 Cloud Foundry 系統監控的 Azure Log Analytics Nozzle

[Azure 監視器](https://azure.microsoft.com/services/log-analytics/) 是 Azure 中的一項服務。 它能協助您收集和分析從雲端及內部部署環境產生的資料。

Log Analytics 噴嘴 (噴嘴) 是 Cloud Foundry (CF) 元件，可將 [Cloud Foundry loggregator](https://docs.cloudfoundry.org/loggregator/architecture.html) firehose 的計量轉送到 Azure 監視器記錄。 在 Nozzle 的協助之下，您可以收集、檢視及分析多個部署的系統健康情況和效能計量。

在本檔中，您將瞭解如何將噴嘴部署到 CF 環境，然後從 Azure 監視器記錄檔主控台存取資料。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Prerequisites

下列步驟是部署 Nozzle 的必要條件。

### <a name="1-deploy-a-cf-or-pivotal-cloud-foundry-environment-in-azure"></a>1. 在 Azure 中部署 CF 或 Pivotal Cloud Foundry 環境

您可以使用 Nozzle 搭配開放原始碼 CF 部署或 Pivotal Cloud Foundry (PCF) 部署。

* [在 Azure 上部署 Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)

* [在 Azure 上部署 Pivotal Cloud Foundry](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2. 安裝 CF 命令列工具來部署噴嘴

Nozzle 在 CF 環境中是作為應用程式執行。 您需要 CF CLI 才能部署應用程式。

Nozzle 也需要 Loggregator Firehose 和 Cloud Controller 的存取權限。 若要建立並設定使用者，您需要使用者帳戶和驗證 (UAA) 服務。

* [安裝 Cloud Foundry CLI](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

* [安裝 Cloud Foundry UAA 命令列用戶端](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md)

設定 UAA 命令列用戶端之前，請確定已安裝 RubyGems。

### <a name="3-create-a-log-analytics-workspace-in-azure"></a>3. 在 Azure 中建立 Log Analytics 工作區

您可以手動方式或使用範本來建立 Log Analytics 工作區。 此範本會為 Azure 監視器記錄檔主控台部署預先設定的 KPI 視圖和警示的設定。 

#### <a name="to-create-the-workspace-manually"></a>若要手動建立工作區：

1. 在 Azure 入口網站中，搜尋 Azure Marketplace 中的服務清單，然後選取 [Log Analytics 工作區]。
2. 選取 [建立]，然後選取下列項目的選項：

   * **Log Analytics 工作區** ：輸入您工作區的名稱。
   * **訂用帳戶** ：如果您擁有多個訂用帳戶，請選擇與 CF 部署相同的訂用帳戶。
   * **資源群組** ：您可以建立新的資源群組，或使用與 CF 部署相同的資源群組。
   * **位置** ：輸入位置。
   *  以完成。

如需詳細資訊，請參閱[開始使用 Azure 監視器記錄](../azure-monitor/overview.md)。

#### <a name="to-create-the-log-analytics-workspace-through-the-monitoring-template-from-azure-market-place"></a>透過來自 Azure Marketplace 的監視範本建立 Log Analytics 工作區：

1. 開啟 Azure 入口網站。
1. 按一下 "+" 號，或左上角的 [建立資源]。
1. 在搜尋視窗中輸入 "Cloud Foundry"，選取 [Cloud Foundry Monitoring Solution]。
1. 這會載入 Cloud Foundry 監視解決方案範本首頁，按一下 [建立] 以啟動範本刀鋒視窗。
1. 輸入必要參數：
    * **訂用帳戶** ：選取 Log Analytics 工作區的 Azure 訂用帳戶，通常與 Cloud Foundry 部署相同。
    * **資源群組** ：選取現有的資源群組，或為 Log Analytics 工作區建立一個新的資源群組。
    * **資源群組位置** ：選取資源群組的位置。
    * **OMS_Workspace_Name** ：如果工作區不存在，請輸入工作區名稱，範本會建立新的工作區。
    * **OMS_Workspace_Region** ：選取工作區的位置。
    * **OMS_Workspace_Pricing_Tier** ：選取 Log Analytics 工作區 SKU。 如需參考，請參閱[定價指引](https://azure.microsoft.com/pricing/details/log-analytics/)。
    * **法律條款** ：按一下 [法律條款]，然後按一下 [建立] 以接受法律條款。
1. 指定所有參數之後，按一下 [建立] 以部署範本。 部署完成時，會在 [通知] 索引標籤中顯示狀態。


## <a name="deploy-the-nozzle"></a>部署 Nozzle

有幾種不同的方式可部署 Nozzle：作為 PCF 圖格或作為 CF 應用程式。

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>將 Nozzle 部署為 PCF Ops Manager 圖格

請遵循 [install and configure the Azure Log Analytics Nozzle for PCF](https://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html) (安裝並設定適用於 PCF 的 Azure Log Analytics Nozzle) 中的步驟進行。此方法經過簡化，PCF Ops Manager 磚會自動設定並推送 Nozzle。 

### <a name="deploy-the-nozzle-manually-as-a-cf-application"></a>以手動方式將 Nozzle 部署為 CF 應用程式

如果您未使用 PCF Ops Manager，請部署 Nozzle 作為應用程式。 下列章節說明此程序。

#### <a name="sign-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>透過 CF CLI 以管理員身分登入 CF 部署

執行下列命令：
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

"SYSTEM_DOMAIN" 是您的 CF 網域名稱。 您可以在 CF 部署資訊清單檔案中搜尋 "SYSTEM_DOMAIN" 來取出。 

"CF_User" 是 CF 管理員名稱。 您可以在 CF 部署資訊清單檔案中搜尋 "scim" 區段，並找尋名稱和 "cf_admin_password" 來取出名稱和密碼。

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>建立 CF 使用者及授與必要權限

執行下列命令：
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

"SYSTEM_DOMAIN" 是您的 CF 網域名稱。 您可以在 CF 部署資訊清單檔案中搜尋 "SYSTEM_DOMAIN" 來取出。

#### <a name="download-the-latest-log-analytics-nozzle-release"></a>下載最新版本的 Log Analytics Nozzle

執行下列命令：
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables"></a>設定環境變數

現在您可以在目前的目錄中，於 manifest.yml 中設定環境變數。 下圖說明 Nozzle 的應用程式資訊清單。 將值取代為您的特定 Log Analytics 工作區資訊。

```
OMS_WORKSPACE             : Log Analytics workspace ID: Open your Log Analytics workspace in the Azure portal, select **Advanced settings**, select **Connected Sources**, and select **Windows Servers**.
OMS_KEY                   : OMS key: Open your Log Analytics workspace in the Azure portal, select **Advanced settings**, select **Connected Sources**, and select **Windows Servers**.
OMS_POST_TIMEOUT          : HTTP post timeout for sending events to Azure Monitor logs. The default is 10 seconds.
OMS_BATCH_TIME            : Interval for posting a batch to Azure Monitor logs. The default is 10 seconds.
OMS_MAX_MSG_NUM_PER_BATCH : The maximum number of messages in a batch to Azure Monitor logs. The default is 1000.
API_ADDR                  : The API URL of the CF environment. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
DOPPLER_ADDR              : Loggregator's traffic controller URL. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
FIREHOSE_USER             : CF user you created in the preceding section, "Create a CF user and grant required privileges." This user has firehose and Cloud Controller admin access.
FIREHOSE_USER_PASSWORD    : Password of the CF user above.
EVENT_FILTER              : Event types to be filtered out. The format is a comma-separated list. Valid event types are METRIC, LOG, and HTTP.
SKIP_SSL_VALIDATION       : If true, allows insecure connections to the UAA and the traffic controller.
CF_ENVIRONMENT            : Enter any string value for identifying logs and metrics from different CF environments.
IDLE_TIMEOUT              : The Keep Alive duration for the firehose consumer. The default is 60 seconds.
LOG_LEVEL                 : The logging level of the Nozzle. Valid levels are DEBUG, INFO, and ERROR.
LOG_EVENT_COUNT           : If true, the total count of events that the Nozzle has received and sent are logged to Azure Monitor logs as CounterEvents.
LOG_EVENT_COUNT_INTERVAL  : The time interval of the logging event count to Azure Monitor logs. The default is 60 seconds.
```

### <a name="push-the-application-from-your-development-computer"></a>從開發電腦推送應用程式

確認您在 oms-log-analytics-firehose-nozzle 資料夾下。 執行下列命令：
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>驗證 Nozzle 安裝

### <a name="from-apps-manager-for-pcf"></a>從 Apps Manager (適用於 PCF)

1. 登入 Ops Manager，確認圖格顯示於安裝儀表板上。
2. 登入 Apps Manager，確認您為 Nozzle 建立的空間列示在使用情況報告中，並確認處於正常狀態。

### <a name="from-your-development-computer"></a>從您的開發電腦

在 CF CLI 視窗中，輸入：
```
cf apps
```
確認 OMS Nozzle 應用程式正在執行。

## <a name="view-the-data-in-the-azure-portal"></a>在 Azure 入口網站中檢視資料

如果您已透過 Marketplace 範本部署監視解決方案，請前往 Azure 入口網站並尋找該解決方案。 您可以在資源群組中找到您在範本中指定的解決方案。 按一下解決方案，流覽至 [log analytics 主控台]，就會列出預先設定的視圖，其中包含最 Cloud Foundry 系統 Kpi、應用程式資料、警示和 VM 健康情況計量。 

如果您已手動建立 Log Analytics 工作區，請依照下列步驟來建立檢視和警示：

### <a name="1-import-the-oms-view"></a>1. 匯入 OMS 視圖

從 OMS 入口網站，流覽至 **View Designer** 匯  >  **入**  >  **流覽** ，然後選取其中一個 omsview 檔案。 例如，選取 Cloud Foundry.omsview  ，然後儲存檢視。 圖格隨即會顯示在 [概觀]  頁面上。 選取以查看視覺化的計量。

您可以透過 **檢視設計工具** 來自訂這些檢視或建立新檢視。

*"Cloud Foundry.omsview"* 是 Cloud Foundry OMS 檢視範本的預覽版本。 這是完整設定的預設範本。 如果您有關於範本的建議或意見反應，請傳送至[問題區段](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues)。

### <a name="2-create-alert-rules"></a>2. 建立警示規則

您可以視需要[建立警示](../azure-monitor/platform/alerts-overview.md)，並自訂查詢和閾值。 以下是建議的警示：

| 搜尋查詢                                                                  | 產生警示的依據 | 描述                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Type=CF_ValueMetric_CL Origin_s=bbs Name_s="Domain.cf-apps"                   | 結果數目 < 1   | **bbs.Domain.cf-apps** 表示 cf-apps 網域是否處於最新狀態。 這表示來自 Cloud Controller 的 CF 應用程式要求已同步至 bbs.LRPsDesired (Diego 所需 AI) 以供執行。 未接收到資料表示 cf-apps 網域在指定時間範圍內不是最新狀態。 |
| Type=CF_ValueMetric_CL Origin_s=rep Name_s=UnhealthyCell Value_d>1            | 結果數目 > 0   | 對於 Diego 資料格來說，0 表示健康情況良好，1 表示健康情況不佳。 請設定警示，使其在指定時間範圍內偵測到多個健康情況不佳的 Diego 資料格時發出。 |
| Type=CF_ValueMetric_CL Origin_s="bosh-hm-forwarder" Name_s="system.healthy" Value_d=0 | 結果數目 > 0 | 1 表示系統健康情況良好，0 表示系統健康情況不佳。 |
| Type=CF_ValueMetric_CL Origin_s=route_emitter Name_s=ConsulDownMode Value_d>0 | 結果數目 > 0   | Consul 會定期發出它的健康情況狀態。 0 表示系統健康情況良好，1 表示路由發射器偵測到 Consul 已關閉。 |
| Type=CF_CounterEvent_CL Origin_s=DopplerServer (Name_s="TruncatingBuffer.DroppedMessages" or Name_s="doppler.shedEnvelopes") Delta_d>0 | 結果數目 > 0 | Doppler 因背部壓力而刻意捨棄的訊息差異數目。 |
| Type=CF_LogMessage_CL SourceType_s=LGR MessageType_s=ERR                      | 結果數目 > 0   | Loggregator 會發出 **LGR** 來指出記錄流程發生的問題。 這類問題的範例是記錄訊息輸出太高。 |
| Type=CF_ValueMetric_CL Name_s=slowConsumerAlert                               | 結果數目 > 0   | 當噴嘴收到來自 loggregator 的緩慢消費者警示時，它會將 **>slowconsumeralert** valuemetric 傳送傳送給 Azure 監視器記錄。 |
| Type=CF_CounterEvent_CL Job_s=nozzle Name_s=eventsLost Delta_d>0              | 結果數目 > 0   | 如果遺失事件的差異數目達到閾值，代表 Nozzle 可能發生執行上的問題。 |

## <a name="scale"></a>調整

您可以調整 Nozzle 和 Loggregator。

### <a name="scale-the-nozzle"></a>調整 Nozzle

您應該從至少兩個 Nozzle 執行個體開始。 Firehose 會將工作負載平均分配給所有 Nozzle 執行個體。
若要確定 Nozzle 可以從 Firehose 跟上資料流量，請設定 **slowConsumerAlert** 的警示 (列在上一節「建立警示規則」中)。 在您收到警示之後，請遵循[緩慢 Nozzle 指引](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz)來判斷是否需要調整。
若要相應增加 Nozzle，請使用 Apps Manager 或 CF CLI 來增加 Nozzle 的執行個體數目或記憶體/磁碟資源。

### <a name="scale-the-loggregator"></a>調整 Loggregator

Loggregator 能傳送 **LGR** 記錄訊息來指出記錄流程發生的問題。 您可以監控警示來判斷 Loggregator 是否需要相應增加。
若要將 Loggregator 相應增加，可以增加 Doppler 緩衝區大小，或是在 CF 資訊清單中新增額外的 Doppler 伺服器執行個體。 如需詳細資訊，請參閱[調整 Loggregator 的指引](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling)。

## <a name="update"></a>更新

若要將 Nozzle 更新為新版本，請下載新版本的 Nozzle，再遵循先前「部署 Nozzle」一節中的步驟，重新推送一次應用程式。

### <a name="remove-the-nozzle-from-ops-manager"></a>移除 Nozzle 從 Ops Manager 移除

1. 登入 Ops Manager。
2. 找出 **PCF 磚的 Microsoft Azure Log Analytics 噴嘴** 。
3. 選取回收圖示並確認刪除。

### <a name="remove-the-nozzle-from-your-development-computer"></a>從您的開發電腦移除 Nozzle

在 CF CLI 視窗中，輸入：
```
cf delete <App Name> -r
```

如果您移除 Nozzle，OMS 入口網站中的資料並不會自動移除。 它會根據您的 Azure 監視器記錄保留設定到期。

## <a name="support-and-feedback"></a>支援與意見反應

Azure Log Analytics Nozzle 已經開啟為來源。 將您的問題和意見反應傳送至 [GitHub 區段](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues)。 若要開啟 Azure 支援要求，請選擇「執行 Cloud Foundry 的虛擬機器」作為服務類別。 

## <a name="next-step"></a>後續步驟

從 PCF 2.0 開始，VM 效能計量會由系統計量轉寄站傳輸到 Azure Log Analytics 噴嘴，並整合至 Log Analytics 工作區。 您不再需要 Log Analytics 代理程式，就能取得 VM 效能計量。 不過，您仍然可以使用 Log Analytics 代理程式來收集 Syslog 資訊。 Log Analytics 代理程式會以 Bosh 附加元件的形式安裝至您的 CF VM。 

如需詳細資料，請參閱[將 Log Analytics 代理程式部署到 Cloud Foundry 部署](https://github.com/Azure/oms-agent-for-linux-boshrelease) \(英文\)。
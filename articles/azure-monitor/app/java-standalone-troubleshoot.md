---
title: 針對 JAVA 的 Azure 監視器 Application Insights 進行疑難排解
description: 瞭解如何針對 Azure 監視器 Application Insights 的 JAVA 代理程式進行疑難排解
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-java
ms.openlocfilehash: 54bf2440dff20fc757f37e3f31a53c57ebd59120
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133186"
---
# <a name="troubleshooting-guide-azure-monitor-application-insights-for-java"></a>疑難排解指南：適用于 JAVA 的 Azure 監視器 Application Insights

在本文中，我們將討論使用 JAVA agent 進行 Application Insights 時，您可能面臨的一些常見問題。 我們也會討論解決這些問題的步驟。 Application Insights 是 Azure 監視器 platform service 的一項功能。

## <a name="check-the-self-diagnostic-log-file"></a>檢查自我診斷記錄檔

根據預設，Application Insights 的 JAVA 3.0 代理程式會產生一個記錄檔， `applicationinsights.log` 該檔案會在保存該檔案的相同目錄中命名 `applicationinsights-agent-3.0.0.jar` 。

此記錄檔是檢查提示是否有您可能遇到之任何問題的第一個位置。

## <a name="jvm-fails-to-start"></a>JVM 無法啟動

如果 JVM 無法啟動，並出現「開啟 zip 檔案或 JAR 資訊清單遺失時發生錯誤」，請嘗試重新下載代理程式 JAR 檔案，因為它在檔案傳輸期間可能已損毀。

## <a name="upgrade-from-the-application-insights-java-2x-sdk"></a>從 Application Insights JAVA 2.x SDK 升級

如果您已經在應用程式中使用 Application Insights JAVA 2.x SDK，您可以繼續使用它。 JAVA 3.0 代理程式將會偵測到它。 如需詳細資訊，請參閱 [從 JAVA 2.X SDK 進行升級](./java-standalone-upgrade-from-2x.md)。

## <a name="upgrade-from-application-insights-java-30-preview"></a>從 Application Insights JAVA 3.0 Preview 升級

如果您要從 JAVA 3.0 Preview 代理程式升級，請仔細檢查所有設定 [選項](./java-standalone-config.md) 。 JSON 結構已在3.0 正式運作 (GA) 版本中完全變更。

這些變更包括：

-  設定檔案名稱已從變更 `ApplicationInsights.json` 為 `applicationinsights.json` 。
-  `instrumentationSettings`節點不再存在。 中的所有內容 `instrumentationSettings` 都會移至根層級。 
-  設定節點（例如 `sampling` 、 `jmxMetrics` 、和） `instrumentation` `heartbeat` 會從 `preview` 根層級移出。

## <a name="some-logging-is-not-auto-collected"></a>不會自動收集某些記錄

只有在第一次符合記錄架構設定的閾值時才會捕獲記錄，第二個則會符合 Application Insights 設定的閾值。

知道特定記錄語句是否符合記錄架構設定的閾值，最好的方法是確認它顯示在您的一般應用程式記錄檔 (例如檔案或主控台) 。

如需詳細資訊，請參閱 [自動收集的記錄](./java-standalone-config.md#auto-collected-logging) 設定。

## <a name="import-ssl-certificates"></a>匯入 SSL 憑證

如果您使用預設的 JAVA 金鑰儲存區，它將已擁有所有 CA 根憑證。 您不應該需要匯入更多 SSL 憑證。

如果您使用自訂 JAVA 金鑰儲存區，您可能需要將 Application Insights 的端點 SSL 憑證匯入其中。

### <a name="key-terminology"></a>重要術語
*金鑰* 儲存區是憑證、公開金鑰和私密金鑰的儲存機制。 JAVA 開發工具組散發套件通常具有可執行檔來管理它們： `keytool` 。

下列範例是將 SSL 憑證匯入金鑰儲存區的簡單命令：

`keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name".cer -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-and-add-an-ssl-certificate"></a>下載和新增 SSL 憑證的步驟

1.  開啟您慣用的瀏覽器，並移至 `IngestionEndpoint` 用來檢測應用程式的連接字串中出現的 URL。

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-url.png" alt-text="顯示 Application Insights 連接字串的螢幕擷取畫面。":::

2.  在瀏覽器中選取 [ **查看網站資訊** (鎖定) ] 圖示，然後選取 [ **憑證** ] 選項。

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="[網站資訊] 中 [憑證] 選項的螢幕擷取畫面。":::

3.  移至 [ **詳細資料** ] 索引標籤，然後選取 [ **複製到** 檔案]。
4.  選取 [ **下一步]** 按鈕，選取 [ **Base-64 編碼的 x.509] (。CER)** 格式，然後再選取 **[下一步]** 。

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="憑證匯出嚮導的螢幕擷取畫面，其中已選取格式。":::

5.  指定您要儲存 SSL 憑證的檔案。 然後選取 **[下一步**  >  **]**。 您應該會看到「匯出成功」訊息。
6.  擁有憑證之後，就可以將憑證匯入至 JAVA 金鑰儲存區。 使用 [上述命令](#key-terminology) 匯入憑證。

> [!WARNING]
> 您必須重複這些步驟，才能在目前的憑證到期之前取得新憑證。 您可以在 [**憑證**] 對話方塊的 [**詳細資料**] 索引標籤上找到到期資訊。
>
> :::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="顯示 SSL 憑證詳細資料的螢幕擷取畫面。":::

---
title: 疑難排解-Azure 監視器 Application Insights JAVA
description: 針對 Azure 監視器 Application Insights JAVA 進行疑難排解
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-java
ms.openlocfilehash: cf27763f857cc1fd1aad5256d0c6cecf91251caf
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855593"
---
# <a name="troubleshooting-azure-monitor-application-insights-java"></a>針對 Azure 監視器 Application Insights JAVA 進行疑難排解

在本文中，我們討論了一些使用者在使用 java 代理程式來檢測 java 應用程式時可能遇到的常見問題，以及解決這些問題的步驟。

## <a name="self-diagnostic-log-file"></a>自我診斷記錄檔

根據預設，Application Insights JAVA 3.0 會產生一個記錄檔，該檔案會 `applicationinsights.log` 在檔案所在的相同目錄中命名 `applicationinsights-agent-3.0.0.jar` 。

此記錄檔是檢查您可能遇到之任何問題提示的第一個位置。

## <a name="upgrade-from-application-insights-java-2x-sdk"></a>從 Application Insights JAVA 2.x SDK 升級

請參閱 [從 2.X SDK 進行升級](./java-standalone-upgrade-from-2x.md)。

## <a name="upgrade-from-30-preview"></a>從3.0 預覽升級

如果您是從 3.0 Preview 升級，請仔細檢查所有的設定 [選項](./java-standalone-config.md) ，因為 3.0 GA 版本中的 json 結構已完全變更。

這些變更包括：

1.  設定檔案名稱本身已從變更 `ApplicationInsights.json` 為 `applicationinsights.json` 。
2.  `instrumentationSettings`節點不再存在。 中的所有內容 `instrumentationSettings` 都會移至根層級。 
3.  設定節點（例如 `sampling` 、 `jmxMetrics` 和） `instrumentation` `heartbeat` 會從 `preview` 根層級移出。

## <a name="ssl-certificate-issues"></a>SSL 憑證問題

如果您使用預設的 JAVA 金鑰儲存區，它將已擁有所有 CA 根憑證，您應該不需要匯入任何進一步的 SSL 憑證。

如果您使用自訂 JAVA 金鑰儲存區，您可能需要將 Application Insights 的端點 SSL 憑證匯入其中。

### <a name="some-key-terminology"></a>一些重要術語：
*金鑰* 儲存區是憑證、公開和私密金鑰的儲存機制。 JDK 散發通常具有可執行檔來管理它們– `keytool` 。

下列範例是將 SSL 憑證匯入金鑰儲存區的簡單命令：

`keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name".cer -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-and-add-the-ssl-certificate"></a>下載並新增 SSL 憑證的步驟：

1.  開啟您慣用的瀏覽器，並移至 `IngestionEndpoint` 用來檢測應用程式的連接字串中出現的 url，如下所示

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-url.png" alt-text="Application Insights 連接字串":::

2.  按一下瀏覽器上的 [查看網站資訊] (鎖定) 圖示，然後按一下 [憑證] 選項，如下所示

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="SSL 憑證捕獲":::

3.  前往 [詳細資料] 索引標籤，然後按一下 [複製到檔案]。
4.  按一下 [下一步] 按鈕，然後選取 [x.509 ( 的 [Base-64 編碼]。CER) ] 格式，然後選取 [下一步]。

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="SSL 憑證 ExportWizard":::

5.  指定您要儲存 SSL 憑證的檔案。 最後按 [下一步]，然後完成。 您應該會看到「匯出成功」訊息。
6.  一旦讓憑證將憑證匯入到 JAVA 金鑰儲存區之後，就可以了。 使用上述 [命令](#some-key-terminology) 匯入憑證。

> [!WARNING]
> 您將需要重複這些步驟，才能在目前的憑證到期之前取得新的憑證。 您可以在憑證快顯視窗的 [詳細資料] 索引標籤中找到到期資訊，如下所示

:::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="SSL 憑證詳細資料":::

---
title: 匯出 Azure Cosmos DB 模擬器憑證
description: 瞭解如何匯出 Azure Cosmos DB 模擬器憑證，以搭配 JAVA、Python 和 Node.js 應用程式使用。 憑證應該匯出並用於不使用 Windows 憑證存放區的語言和執行時間環境。
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/17/2020
author: deborahc
ms.author: dech
ms.custom: devx-track-python, devx-track-java, contperfq1
ms.openlocfilehash: f747cc6405cef07510766bcfa4c826aec9d9857b
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92490402"
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs-apps"></a>匯出 Azure Cosmos DB 模擬器憑證以與 JAVA、Python 和 Node.js 應用程式搭配使用

Azure Cosmos DB 模擬器提供了一個模擬 Azure Cosmos DB 服務的本機環境，以供開發之用。 Azure Cosmos 模擬器僅支援透過 TLS 連接的安全通訊。

當您第一次執行模擬器時，會產生 Azure Cosmos DB 本機模擬器中的憑證。 會產生兩個憑證； 其中一個用來連接到本機模擬器，另一個用來管理模擬器內模擬器資料的預設加密。 您要匯出的憑證是有 "DocumentDBEmulatorCertificate" 易記名稱的連線憑證。

當您使用模擬器以不同的語言（例如 JAVA、Python 或 Node.js）開發應用程式時，您需要匯出模擬器憑證，並將它匯入至所需的憑證存放區。

當應用程式在 Windows OS 主機上執行時，.NET 語言和執行時間會使用 Windows 憑證存放區來安全地連線至 Azure Cosmos DB 本機模擬器。 其他語言則有自己管理和使用憑證的方法。 例如，JAVA 使用自己的 [憑證存放區](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html)、Python 使用 [通訊端包裝](https://docs.python.org/2/library/ssl.html)函式，Node.js 使用 [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback)。

本文示範如何匯出 TLS/SSL 憑證，以用於不與 Windows 憑證存放區整合的不同語言與執行時間環境。 您可以閱讀[使用 Azure Cosmos DB 模擬器進行開發和測試](./local-emulator.md)，了解更多有關模擬器的資訊。

## <a name="export-the-azure-cosmos-db-tlsssl-certificate"></a><a id="export-emulator-certificate"></a>匯出 Azure Cosmos DB TLS/SSL 憑證

您需要匯出模擬器憑證，才能成功地從未與 Windows 憑證存放區整合的語言和執行時間環境中使用模擬器端點。 您可以使用 Windows 憑證管理員來匯出憑證。 使用下列逐步指示，將 "有 documentdbemulatorcertificate" 憑證匯出為 BASE-64 編碼的 x.509 ( .cer) 檔：

1. 執行 certlm.msc 啟動 Windows 憑證管理員，瀏覽至 [個人 -> 憑證] 資料夾並開啟有 **DocumentDbEmulatorCertificate** 易記名稱的憑證。

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png" alt-text="Azure Cosmos DB 本機模擬器的匯出步驟 1":::

1. 按一下 [詳細資料]****，然後按一下 [確定]****。

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png" alt-text="Azure Cosmos DB 本機模擬器的匯出步驟 1":::

1. 按一下 [複製到檔案...]****。

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-3.png" alt-text="Azure Cosmos DB 本機模擬器的匯出步驟 1":::

1. 按 [下一步]  。

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-4.png" alt-text="Azure Cosmos DB 本機模擬器的匯出步驟 1":::

1. 按一下 [否，不要匯出私密金鑰 ]****，然後按 [下一步]****。

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-5.png" alt-text="Azure Cosmos DB 本機模擬器的匯出步驟 1":::

1. 按一下 [Base-64 編碼的 X.509 (.CER)]****，然後按 [下一步]****。

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-6.png" alt-text="Azure Cosmos DB 本機模擬器的匯出步驟 1":::

1. 指定憑證的名稱。 此案例中為 **documentdbemulatorcert**，然後按 [下一步]****。

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-7.png" alt-text="Azure Cosmos DB 本機模擬器的匯出步驟 1":::

1. 按一下 [完成] 。

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-8.png" alt-text="Azure Cosmos DB 本機模擬器的匯出步驟 1":::

## <a name="use-the-certificate-with-java-apps"></a>搭配使用憑證與 JAVA 應用程式

執行 JAVA 應用程式或使用以 JAVA 為基礎的用戶端應用程式時，將憑證安裝到 JAVA 預設憑證存放區比傳遞 `-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>"` 旗標更容易。 例如，包含的 JAVA 示範應用程式 (`https://localhost:8081/_explorer/index.html`) 取決於預設的憑證存放區。

遵循將 [憑證新增至 JAVA 憑證存放區](/azure/developer/java/sdk/java-sdk-add-certificate-ca-store) 中的指示，將 x.509 憑證匯入至預設的 java 憑證存放區。 請記住，當您執行 keytool 時，將會在 *% JAVA_HOME%* 目錄中工作。 將憑證匯入憑證存放區之後，適用于 MongoDB 的 SQL 和 Azure Cosmos DB 的 API 用戶端將能夠連線至 Azure Cosmos 模擬器。

或者，您可以執行下列 bash 腳本來匯入憑證：

```bash
#!/bin/bash

# If emulator was started with /AllowNetworkAccess, replace the below with the actual IP address of it:
EMULATOR_HOST=localhost
EMULATOR_PORT=8081
EMULATOR_CERT_PATH=/tmp/cosmos_emulator.cert
openssl s_client -connect ${EMULATOR_HOST}:${EMULATOR_PORT} </dev/null | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > $EMULATOR_CERT_PATH
# delete the cert if already exists
sudo $JAVA_HOME/bin/keytool -cacerts -delete -alias cosmos_emulator
# import the cert
sudo $JAVA_HOME/bin/keytool -cacerts -importcert -alias cosmos_emulator -file $EMULATOR_CERT_PATH
```

安裝 "CosmosDBEmulatorCertificate" TLS/SSL 憑證之後，您的應用程式應該能夠連接並使用本機 Azure Cosmos DB 模擬器。 如果您有任何問題，您可以遵循「偵測 [SSL/TLS 連接](https://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html) 」一文。 在大部分情況下，憑證可能不會安裝到 *% JAVA_HOME%/jre/lib/security/cacerts* 存放區中。 例如，如果您有多個已安裝的 JAVA 版本，您的應用程式可能會使用與您所更新的不同 cacerts 存放區。

## <a name="use-the-certificate-with-python-apps"></a>使用 Python 應用程式的憑證

從 Python 應用程式連線至模擬器時，TLS 驗證已停用。 根據預設，在連線到本機模擬器時， [PYTHON SDK (version 2.0.0 或更高版本 ](sql-api-sdk-python.md) 的 SQL API) 不會嘗試使用 TLS/SSL 憑證。 但是，如果您想要使用 TLS 驗證，可以遵循 [Python 通訊端包裝](https://docs.python.org/2/library/ssl.html) 檔中的範例。

## <a name="how-to-use-the-certificate-in-nodejs"></a>如何在 Node.js 中使用憑證

從 Node.js Sdk 連接到模擬器時，會停用 TLS 驗證。 根據預設，SQL API 的 [Node.js SDK (1.10.1 版或更高版本) ](sql-api-sdk-node.md) 不會在連線到本機模擬器時嘗試使用 TLS/SSL 憑證。 但是，如果您想要使用 TLS 驗證，可以遵循 [Node.js 檔](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback)中的範例。

## <a name="rotate-emulator-certificates"></a>輪替模擬器憑證

您可以從 Windows 系統匣中執行的 Azure Cosmos DB 模擬器中選取 [ **重設資料** ]，以強制重新產生模擬器憑證。 請注意，此動作也會清除模擬器儲存在本機的所有資料。

:::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png" alt-text="Azure Cosmos DB 本機模擬器的匯出步驟 1":::

如果您已將憑證安裝到 JAVA 憑證存放區或在其他位置使用，則需要使用目前的憑證重新匯入。 在您更新憑證之前，您的應用程式無法連接到本機模擬器。

## <a name="next-steps"></a>後續步驟

* [使用命令列參數和 PowerShell 命令來控制模擬器](emulator-command-line-parameters.md)
* [模擬器的偵錯工具問題](troubleshoot-local-emulator.md)
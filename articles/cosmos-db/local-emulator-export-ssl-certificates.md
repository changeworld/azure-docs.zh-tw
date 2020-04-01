---
title: 匯出 Azure Cosmos DB 模擬器憑證
description: 使用不使用 Windows 憑證儲存的語言和執行時進行開發時,需要匯出和管理 TLS/SSL 憑證。 這篇文章提供逐步指示。
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
author: deborahc
ms.author: dech
ms.openlocfilehash: b4283ea7d500ca038d9f1cade89c772880ece199
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409071"
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs"></a>匯出 Azure Cosmos DB 模擬器憑證以與 Java、Python 和 Node.js 搭配使用

[**下載模擬器**](https://aka.ms/cosmosdb-emulator)

Azure Cosmos DB 模擬器提供了一個本地環境,用於類比 Azure Cosmos DB 服務,用於開發目的,包括使用 TLS 連接。 這篇文章演示如何匯出 TLS/SSL 憑證,用於不使用 Windows 憑證儲存的語言和運行時,例如使用其自己的[憑證儲存](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html)的 Java 和使用[套接字包裝器](https://docs.python.org/2/library/ssl.html)的 Python 和使用[tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback)的 Node.js。 您可以閱讀[使用 Azure Cosmos DB 模擬器進行開發和測試](./local-emulator.md)，了解更多有關模擬器的資訊。

本教學課程涵蓋下列工作：

> [!div class="checklist"]
> * 輪替憑證
> * 匯出 TLS/SSL 憑證
> * 了解如何使用 Java、Python 和 Node.js 中的憑證

## <a name="certification-rotation"></a>憑證旋轉

Azure Cosmos DB 本機模擬器中的憑證會在模擬器第一次執行時產生。 會產生兩個憑證； 一個用來連線到本機模擬器，一個用來管理模擬器中的機密資料。 您要匯出的憑證是有 "DocumentDBEmulatorCertificate" 易記名稱的連線憑證。

您可以從 Windows 系統匣中執行的 Azure Cosmos DB 模擬器按一下 [重設資料]**** 來重新產生這兩個憑證，如下所示。 如果您重新產生憑證並將這些憑證安裝到 Java 憑證存放區或在其他地方使用它們，則必須更新這些憑證，否則您的應用程式將無法再連線到本機模擬器。

![Azure Cosmos DB 本機模擬器的重設資料](./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png)

## <a name="how-to-export-the-azure-cosmos-db-tlsssl-certificate"></a>如何匯出 Azure 宇宙 DB TLS/SSL 憑證

1. 執行 certlm.msc 啟動 Windows 憑證管理員，瀏覽至 [個人 -> 憑證] 資料夾並開啟有 **DocumentDbEmulatorCertificate** 易記名稱的憑證。

    ![Azure Cosmos DB 本機模擬器的匯出步驟 1](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png)

2. 按一下 [詳細資料]****，然後按一下 [確定]****。

    ![Azure Cosmos DB 本機模擬器的匯出步驟 2](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png)

3. 按一下 [複製到檔案...]****。

    ![Azure Cosmos DB 本機模擬器的匯出步驟 3](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-3.png)

4. 按 [下一步]  。

    ![Azure Cosmos DB 本機模擬器的匯出步驟 4](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-4.png)

5. 按一下 [否，不要匯出私密金鑰 ]****，然後按 [下一步]****。

    ![Azure Cosmos DB 本機模擬器的匯出步驟 5](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-5.png)

6. 按一下 [Base-64 編碼的 X.509 (.CER)]****，然後按 [下一步]****。

    ![Azure Cosmos DB 本機模擬器的匯出步驟 6](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-6.png)

7. 指定憑證的名稱。 此案例中為 **documentdbemulatorcert**，然後按 [下一步]****。

    ![Azure Cosmos DB 本機模擬器的匯出步驟 7](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-7.png)

8. 按一下 **[完成]**。

    ![Azure Cosmos DB 本機模擬器的匯出步驟 8](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-8.png)

## <a name="how-to-use-the-certificate-in-java"></a>如何在 Java 中使用憑證

執行 Java 應用程式或執行使用 Java 用戶端的 MongoDB 應用程式時，將憑證安裝到 Java 預設憑證存放區比傳遞 `-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>"` 旗標更簡單。 例如包含的 [Java 示範應用程式](https://localhost:8081/_explorer/index.html)會根據預設的憑證存放區。

遵循[新增憑證至 Java CA 憑證存放區](https://docs.microsoft.com/azure/java-add-certificate-ca-store)中的指示，將 X.509 憑證匯入預設的 Java 憑證存放區。 請記住，執行 keytool 時您會在 %JAVA_HOME% 目錄中工作。

安裝「宇宙DBE模擬器證書」TLS/SSL證書後,應用程式應該能夠連接和使用本地 Azure Cosmos DB 模擬器。 如果繼續遇到問題,可能需要遵循[調試 SSL/TLS 連接](https://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html)一文。 很有可能是未將憑證安裝至 %JAVA_HOME%/jre/lib/security/cacerts 存放區。 例如，如果您安裝多個 Java 版本，您的應用程式使用的 cacerts 存放區可能與您更新的不同。

## <a name="how-to-use-the-certificate-in-python"></a>如何在 Python 中使用憑證

預設情況下,SQL API 的[Python SDK(版本 2.0.0 或更高版本)](sql-api-sdk-python.md)在連接到本地模擬器時不會嘗試使用 TLS/SSL 憑證。 但是,如果要使用 TLS 驗證,可以按照[Python 套接字包裝器](https://docs.python.org/2/library/ssl.html)文檔中的範例進行操作。

## <a name="how-to-use-the-certificate-in-nodejs"></a>如何在 Node.js 中使用憑證

預設情況下,SQL API 的[Node.js SDK(版本 1.10.1 或更高版本)](sql-api-sdk-node.md)在連接到本地模擬器時不會嘗試使用 TLS/SSL 證書。 但是,如果您想要使用 TLS 驗證,您可以按照[Node.js 文件中](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback)的範例操作。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已完成下列操作：

> [!div class="checklist"]
> * 輪替憑證
> * 匯出 TLS/SSL 憑證
> * 了解如何使用 Java、Python 和 Node.js 中的憑證

您現在可以繼續進行＜概念＞一節，了解有關 Azure Cosmos DB 的詳細資訊。 

> [!div class="nextstepaction"]
>[Azure Cosmos DB 中的 Tunable 資料一致性層級](../cosmos-db/consistency-levels.md)

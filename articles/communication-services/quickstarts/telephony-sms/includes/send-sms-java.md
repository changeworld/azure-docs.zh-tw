---
title: 包含檔案
description: 包含檔案
services: azure-communication-services
author: chrwhit
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: chrwhit
ms.openlocfilehash: c11c2098d30ed6f00d94124fd77c2ebdb6cd2c7a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91303301"
---
藉由使用通訊服務 Java SMS 用戶端程式庫來傳送 SMS 訊息，以開始使用 Azure 通訊服務。

完成本快速入門後，您的 Azure 帳戶中會產生幾美分或更少的少許費用。

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-net-pr/tree/feature/communication/sdk/communication/Azure.Communication.Sms#todo-update-to-public) | [Artifact (Maven)](#todo-nuget) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true) 第 8 版或更新版本。
- [Apache Maven](https://maven.apache.org/download.cgi)。
- 作用中的 Azure 通訊服務資源和連接字串。 [建立通訊服務資源](../../create-communication-resource.md)。
- 已啟用 SMS 的電話號碼。 [取得電話號碼](../get-phone-number.md)。

### <a name="prerequisite-check"></a>先決條件檢查

- 在終端機或命令視窗中執行 `mvn -v`，確認已安裝 Maven。
- 若要檢視與您通訊服務資源相關聯的電話號碼，請登入 [Azure 入口網站](https://portal.azure.com/)、尋找您的通訊服務資源，然後從左側瀏覽窗格開啟 [電話號碼] 索引標籤。

## <a name="setting-up"></a>設定

### <a name="create-a-new-java-application"></a>建立新的 Java 應用程式

開啟您的終端機或命令視窗，然後瀏覽至您想要在其中建立 Java 應用程式的目錄。 執行下列命令，以從 maven-archetype-quickstart 範本產生 Java 專案。

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

「產生」目標會使用與 artifactId 相同的名稱來建立目錄。 在此目錄下，**src/main/java** 目錄包含專案原始程式碼，**src/test/java 目錄** 包含測試來源，而 **pom.xml** 檔案是專案的「專案物件模型」(簡稱 POM)。

### <a name="install-the-package"></a>安裝套件

在文字編輯器中開啟 **pom.xml** 檔案。 將下列相依性元素加入至相依性群組。

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-sms</artifactId>
    <version>1.0.0-beta.1</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>設定應用程式架構

將 `azure-core-http-netty` 相依性新增至您的 **pom.xml** 檔案。

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-core-http-netty</artifactId>
    <version>1.3.0</version>
</dependency>
```

在文字編輯器中開啟 **/src/main/java/com/communication/quickstart/App.java**、新增匯入指示詞，並移除 `System.out.println("Hello world!");` 陳述式：

```java
package com.communication.quickstart;

import java.io.IOException;
import java.security.InvalidKeyException;
import java.security.NoSuchAlgorithmException;
import java.util.ArrayList;
import java.util.List;

import com.azure.communication.common.CommunicationClientCredential;
import com.azure.communication.common.PhoneNumber;
import com.azure.communication.sms.SmsClient;
import com.azure.communication.sms.SmsClientBuilder;
import com.azure.communication.sms.models.SendSmsOptions;
import com.azure.communication.sms.models.SendSmsResponse;
import com.azure.core.http.HttpClient;
import com.azure.core.http.netty.NettyAsyncHttpClientBuilder;

public class App
{
    public static void main( String[] args ) throws IOException, NoSuchAlgorithmException, InvalidKeyException
    {
        // Quickstart code goes here
    }
}

```

## <a name="object-model"></a>物件模型

下列類別和介面會處理 Azure 通訊服務 SMS 用戶端程式庫的一些主要 Java 功能。

| Name                                                             | 描述                                                                                     |
| ---------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| SmsClientBuilder              | 這個類別會建立 SmsClient。 您可以為其提供端點、認證和 HTTP 用戶端。 |
| SmsClient                    | 這是所有 SMS 功能所需的類別。 您可以使用此類別來傳送 SMS 訊息。                |
| SendSmsResponse               | 此類別包含來自 SMS 服務的回應。                                          |
| CommunicationClientCredential | 這個類別會處理簽署要求。                                                            |
| PhoneNumber                   | 此類別會保存電話號碼資訊

## <a name="authenticate-the-client"></a>驗證用戶端

使用連接字串具現化 `SmsClient`。 下列程式碼會從名為 `COMMUNICATION_SERVICES_ENDPOINT_STRING` 和 `COMMUNICATION_SERVICES_CREDENTIAL_STRING` 的環境變數擷取資源的端點和認證字串 (認證是來自 Azure 入口網站的 `Key`)。 了解如何[管理資源的連接字串](../../create-communication-resource.md#store-your-connection-string)。

將下列程式碼加入 `main` 方法：

```java
// Create an HttpClient builder of your choice and customize it
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

CommunicationClientCredential credential = new CommunicationClientCredential(accessKey);

// Configure and build a new SmsClient
SmsClient client = new SmsClientBuilder()
    .endpoint(endpoint)
    .credential(credential)
    .httpClient(httpClient)
    .buildClient();
```

您可以使用會實作 `com.azure.core.http.HttpClient` 介面的任何自訂 HTTP 用戶端來初始化用戶端。 上述程式碼示範如何使用 `azure-core` 所提供的 [Azure Core Netty HTTP 用戶端](https://docs.microsoft.com/java/api/overview/azure/core-http-netty-readme?view=azure-java-stable&preserve-view=true)。

## <a name="send-an-sms-message"></a>傳送 SMS 訊息

藉由呼叫 sendMessage 方法來傳送 SMS 訊息。 將此程式碼加入到 `main` 方法的結尾處：

```java
List<PhoneNumber> to = new ArrayList<PhoneNumber>();
to.add(new PhoneNumber("<to-phone-number>"));

// SendSmsOptions is an optional field. It can be used
// to enable a delivery report to the Azure Event Grid
SendSmsOptions options = new SendSmsOptions();
options.setEnableDeliveryReport(true);

// Send the message and check the response for a message id
SendSmsResponse response = client.sendMessage(
    new PhoneNumber("<leased-phone-number>"),
    to,
    "<message-text>",
    options);

System.out.println("MessageId: " + response.getMessageId());
```

您應該將 `<leased-phone-number>` 取代為與您通訊服務資源相關聯且已啟用 SMS 的電話號碼，並使用您想要對其傳送訊息的電話號碼取代 `<to-phone-number>`。 所有的電話號碼參數都應該遵守 [E-164 標準](../../../concepts/telephony-sms/plan-solution.md#optional-reading-international-public-telecommunication-numbering-plan-e164)。

`enableDeliveryReport` 參數是選擇性參數，可讓您用來設定傳遞報告。 這適用於想要在傳遞 SMS 訊息時發出事件的案例。 請參閱[處理 SMS 事件](../handle-sms-events.md)快速入門，以設定 SMS 訊息的傳遞報告。

<!--todo: the signature of the `sendMessage` method changes when configuring delivery reporting. Need to confirm that this is how our client library is to be used.-->

## <a name="run-the-code"></a>執行程式碼

瀏覽至包含 **pom.xml** 檔案的目錄，然後使用 `mvn` 命令來編譯專案。

```console

mvn compile

```

接著，建置封裝。

```console

mvn package

```

執行下列 `mvn` 命令以執行應用程式。

```console

mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false

```

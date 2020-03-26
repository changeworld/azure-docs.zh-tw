---
title: 執行多個相依服務：Java 和 Visual Studio Code
services: azure-dev-spaces
ms.date: 11/21/2018
ms.topic: tutorial
description: 本教學課程說明如何使用 Azure Dev Spaces 和 Visual Studio Code，在 Azure Kubernetes Service 上對多服務 Java 應用程式進行偵錯
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 容器, Helm, 服務網格, 服務網格路由傳送, kubectl, k8s
ms.openlocfilehash: beab91964cab9938a5d63584089326bb408f6efc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "75438337"
---
# <a name="running-multiple-dependent-services-java-and-visual-studio-code-with-azure-dev-spaces"></a>執行多個相依服務：搭配 Azure Dev Spaces 使用 Java 和 Visual Studio Code

在本教學課程中，您將了解如何使用 Azure Dev Spaces 來開發多服務應用程式，以及享有 Dev Spaces 提供的一些附加好處。

## <a name="call-a-service-running-in-a-separate-container"></a>呼叫在個別容器中執行的服務

在本節中，您會建立第二個服務 `mywebapi`，並由 `webfrontend` 加以呼叫。 每個服務都會在個別的容器中執行。 然後，您將在這兩個容器間進行偵錯。

![多個容器](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>下載 *mywebapi* 的範例程式碼
為了節省時間，我們將從 GitHub 存放庫下載範例程式碼。 請移至 https://github.com/Azure/dev-spaces ，然後選取 [複製或下載]  ，從 GitHub 存放庫進行下載。 本節的程式碼位於 `samples/java/getting-started/mywebapi`。

### <a name="run-mywebapi"></a>執行 *mywebapi*
1. 在個別的 VS Code 視窗  中開啟資料夾 `mywebapi`。
1. 開啟 [命令選擇區]  (使用 [檢視 | 命令選擇區]  功能表)，然後使用自動完成功能以輸入及選取以下命令：`Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`。
1. 按 F5，並等候服務進行建置和部署。 當偵錯主控台中出現類似下列訊息時，您將會知道其已準備就緒：

    ```cmd
    2019-03-11 17:02:35.935  INFO 216 --- [           main] com.ms.sample.mywebapi.Application       : Started Application in 8.164 seconds (JVM running for 9.272)
    ```

1. 端點 URL 看起來會像是 `http://localhost:<portnumber>`。 **提示：VS Code 狀態列會變成橘色，並顯示可點按的 URL。** 容器可能看起來像在本機執行，但實際是在 Azure 的開發人員空間中執行。 localhost 位址的原因是因為 `mywebapi` 並未定義任何公用端點，而只能從 Kubernetes 執行個體中存取。 為了方便您操作以及與本機電腦上的私人服務互動，Azure 開發人員空間會建立暫存的 SSH 通道，連到在 Azure 中執行的容器。
1. 當 `mywebapi` 就緒時，請開啟瀏覽器並進入 localhost 位址。
1. 如果所有步驟都已成功，您應該會看到 `mywebapi` 服務的回應。

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>從 webfrontend  發出對 mywebapi  的要求
我們現在要在 `webfrontend` 中撰寫對 `mywebapi` 發出要求的程式碼。
1. 切換至 `webfrontend` 的 VS Code 視窗。
1. 在 `package` 陳述式下方新增  下列 `import` 陳述式：

   ```java
   import java.io.*;
   import java.net.*;
   ```
1. 取代  問候語方法的程式碼：

    ```java
    @RequestMapping(value = "/greeting", produces = "text/plain")
    public String greeting(@RequestHeader(value = "azds-route-as", required = false) String azdsRouteAs) throws Exception {
        URLConnection conn = new URL("http://mywebapi/").openConnection();
        conn.setRequestProperty("azds-route-as", azdsRouteAs); // propagate dev space routing header
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(conn.getInputStream())))
        {
            return "Hello from webfrontend and " + reader.lines().reduce("\n", String::concat);
        }
    }
    ```

上述程式碼範例會將傳入要求中的 `azds-route-as` 標頭轉送至傳出要求。 您稍後將了解這對小組的共同開發有何幫助。

### <a name="debug-across-multiple-services"></a>在多個服務間進行偵錯
1. 此時，`mywebapi` 仍應使用附加的偵錯工具執行中。 如果不是，在 `mywebapi` 專案中按 F5。
1. 在 `mywebapi` 專案的 `index()` 方法中設定中斷點，設定在 [`Application.java` 的第 19 行](https://github.com/Azure/dev-spaces/blob/master/samples/java/getting-started/mywebapi/src/main/java/com/ms/sample/mywebapi/Application.java#L19)上
1. 在 `webfrontend` 專案中，請在以 `try` 為開頭的行上，將中斷點設定在傳送 GET 要求給 `mywebapi` 前。
1. 在 `webfrontend` 專案中按 F5 (如果目前正在執行，則重新啟動偵錯工具)。
1. 叫用 Web 應用程式，並在這兩項服務中逐步執行程式碼。
1. 在 Web 應用程式中，[關於] 頁面會顯示由兩項服務串連的訊息：「來自 webfrontend 的 Hello 和來自 mywebapi 的 Hello」。

### <a name="well-done"></a>做得好！
現在，您已有每個容器可以個別開發和部署的多容器應用程式。


## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解 Dev Spaces 中的小組開發](team-development-java.md)

---
title: 如何從 Twilio 撥打電話 (Java) | Microsoft Docs
description: 了解如何在 Azure 上的 Java 應用程式中使用 Twilio 從網頁撥打電話。
services: ''
documentationcenter: java
author: georgewallace
ms.assetid: 0381789e-e775-41a0-a784-294275192b1d
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 168ec65cfd0ff4e87c33324daa353b554111c8aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73838561"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-java-application-on-azure"></a>如何在 Azure 上的 Java 應用程式中使用 Twilio 撥打電話
下列範例將說明如何從 Azure 代管的網頁上使用 Twilio 撥打電話。 生成的應用程式將提示使用者撥打電話值，如下圖所示。

![Azure Call Form Using Twilio and Java][twilio_java]

您必須執行下列動作才能使用本主題中的程式碼：

1. 取得 Twilio 帳戶和驗證權杖。 要開始使用 Twilio，請評估[https://www.twilio.com/pricing][twilio_pricing]定價。 您可以在 註冊[https://www.twilio.com/try-twilio][try_twilio]。 有關 Twilio 提供的 API 的資訊，[https://www.twilio.com/api][twilio_api]請參閱。
2. 取得 Twilio JAR。 在[https://github.com/twilio/twilio-java][twilio_java_github]，您可以下載 GitHub 源並創建自己的 JAR，或下載預構建的 JAR（無論是否具有依賴項）。
   本主題中的程式碼是以預先建置的 TwilioJava-3.3.8-with-dependencies JAR 撰寫的。
3. 將 JAR 新增至您的 Java 建置路徑。
4. 如果您使用 Eclipse 建立此 Java 應用程式，請使用 Eclipse 的部署組件功能在應用程式部署檔案 (WAR) 中加入 Twilio JAR。 如果您並非使用 Eclipse 建立此 Java 應用程式，請確定 Twilio JAR 與您的 Java 應用程式包含在相同的 Azure 角色內，且已新增至應用程式的類別路徑。
5. 確定您的 cacerts 金鑰存放區包含 Equifax Secure Certificate Authority 憑證，且具有 MD5 指模 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (序號為 35:DE:F4:CF，SHA1 指模為 D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A)。 這是[https://api.twilio.com][twilio_api_service]服務的憑證授權單位 （CA） 證書，當您使用 Twilio API 時調用該證書。 如需將此 CA 憑證新增至 JDK cacert 存放區的相關資訊，請參閱[新增憑證至 Java CA 憑證存放區][add_ca_cert]。

此外，強烈建議您熟悉[使用 Eclipse 的 Azure 工具組建立 Hello World 應用程式][azure_java_eclipse_hello_world]上的資訊，或熟悉其他在 Azure 中代管 Java 應用程式的技術 (如果您未使用 Eclipse)。

## <a name="create-a-web-form-for-making-a-call"></a>建立用以撥打電話的 Web 表單
下列程式碼將說明如何建立 Web 表單，以擷取撥打電話所需的使用者資料。 在此範例中，我們新建立了名為 **TwilioCloud** 的動態 Web 專案，並將 **callform.jsp** 新增為 JSP 檔案。

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Automated call form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Make this call</b>.</p>
     <br/>
      <form action="makecall.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="callTo" value="" />
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="callFrom" value="" />
           </td>
         </tr>
         <tr>
           <td>Call message:</td>
           <td><input type="text" size=400 name="callText" value="Hello. This is the call text. Good bye." />
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Make this call" />
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## <a name="create-the-code-to-make-the-call"></a>建立用以撥打電話的程式碼
下列程式碼會在使用者完成 callform.jsp 所顯示的表單時受到呼叫，可用來建立通話訊息及產生通話。 在此範例中，我們將名為 **makecall.jsp** 的 JSP 檔案新增至 **TwilioCloud** 專案。 (在下方的程式碼中，請使用您的 Twilio 帳戶和驗證權杖，而不要使用指派給 **accountSID** 和 **authToken** 的預留位置值。)

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    import="java.util.*"
    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
    pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Call processing happens here</title>
    </head>
    <body>
        <b>This is my make call page.</b><p/>
     <%
    try 
    {
         // Use your account SID and authentication token instead
         // of the placeholders shown here.
         String accountSID = "your_twilio_account";
         String authToken = "your_twilio_authentication_token";

         // Instantiate an instance of the Twilio client.     
         TwilioRestClient client;
         client = new TwilioRestClient(accountSID, authToken);

         // Retrieve the account, used later to retrieve the CallFactory.
         Account account = client.getAccount();

         // Display the client endpoint. 
         out.println("<p>Using Twilio endpoint " + client.getEndpoint() + ".</p>");

         // Display the API version.
         String APIVERSION = TwilioRestClient.DEFAULT_VERSION;
         out.println("<p>Twilio client API version is " + APIVERSION + ".</p>");

         // Retrieve the values entered by the user.
         String callTo = request.getParameter("callTo");  
         // The Outgoing Caller ID, used for the From parameter,
         // must have previously been verified with Twilio.
         String callFrom = request.getParameter("callFrom");
         String userText = request.getParameter("callText");

         // Replace spaces in the user's text with '%20', 
         // to make the text suitable for a URL.
         userText = userText.replace(" ", "%20");

         // Create a URL using the Twilio message and the user-entered text.
         String Url="https://twimlets.com/message";
         Url = Url + "?Message%5B0%5D=" + userText;

         // Display the message URL.
         out.println("<p>");
         out.println("The URL is " + Url);
         out.println("</p>");

         // Place the call From, To and URL values into a hash map. 
         HashMap<String, String> params = new HashMap<String, String>();
         params.put("From", callFrom);
         params.put("To", callTo);
         params.put("Url", Url);

         CallFactory callFactory = account.getCallFactory();
         Call call = callFactory.create(params);
         out.println("<p>Call status: " + call.getStatus()  + "</p>"); 
    } 
    catch (TwilioRestException e) 
    {
        out.println("<p>TwilioRestException encountered: " + e.getMessage() + "</p>");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    catch (Exception e) 
    {
        out.println("<p>Exception encountered: " + e.getMessage() + "");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    %>
    </body>
    </html>

除了撥打電話以外，makecall.jsp 也會顯示 Twilio 端點、API 版本和通話狀態。 例如以下螢幕截圖：

![Azure Call Response Using Twilio and Java][twilio_java_response]

## <a name="run-the-application"></a>執行應用程式
以下是執行應用程式的高階步驟；如需這些步驟的詳細資訊，請參閱[使用 Eclipse 的 Azure 工具組建立 Hello World 應用程式][azure_java_eclipse_hello_world]。

1. 將您的 TwilioCloud WAR 匯出至 Azure **approot** 資料夾。 
2. 修改 **startup.cmd** ，以將 TwilioCloud WAR 解壓縮。
3. 針對計算模擬器編譯您的應用程式。
4. 在計算模擬器中開始進行部署。
5. 開啟瀏覽器並執行 `http://localhost:8080/TwilioCloud/callform.jsp`。
6. 輸入表單中的值，按一下 [Make this call] ****，然後在 makecall.jsp 中檢視結果。

當您做好部署至 Azure 的準備後，您可以針對雲端環境重新編譯部署、部署至 Azure，以及在瀏覽器中執行 http://*your_hosted_name*.cloudapp.net/TwilioCloud/callform.jsp (請將 *your_hosted_name* 取代為您的值)。

## <a name="next-steps"></a>後續步驟
此程式可說明在 Azure 上的 Java 中使用 Twilio 的基本功能。 在部署至生產環境中的 Azure 之前，您可以新增更多錯誤處理或其他功能。 例如：

* 除了使用 Web 表單以外，您也可以使用 Azure 儲存體 Blob 或 SQL Database 來儲存電話號碼和通話文字。 如需在 Java 中使用 Azure 儲存體 Blob 的相關資訊，請參閱[如何使用 Java 的 Blob 儲存體服務][howto_blob_storage_java]。 
* 您可以使用 **RoleEnvironment.getConfigurationSettings** ，從部署的組態設定中擷取 Twilio 帳戶 ID 和驗證權杖，而不要在 makecall.jsp 中進行值的硬式編碼。 有關**角色環境**類的資訊，請參閱在[JSP 中使用 Azure 服務運行時庫][azure_runtime_jsp]。
* makecall.jsp 代碼為**Url**變數分配 Twilio[https://twimlets.com/message][twimlet_message_url]提供的 URL。 此 URL 會提供 Twilio 標記語言 (TwiML) 回應，告知 Twilio 應如何執行通話。 例如，返回的 TwiML 可以包含一個**&lt; &gt; Say**謂詞，該謂詞會導致文本與呼叫接收者通話。 除了使用 Twilio 提供的 URL 以外，您也可以建置自己的服務來回應 Twilio 的要求；如需詳細資訊，請參閱[如何在 Java 中透過 Twilio 使用語音和簡訊功能][howto_twilio_voice_sms_java]。 有關 TwiML 的更多資訊，請訪問[https://www.twilio.com/docs/api/twiml][twiml]，有關**&lt;Say&gt;** 和其他 Twilio 動詞的詳細資訊，請訪問 。 [https://www.twilio.com/docs/api/twiml/say][twilio_say]
* 在 上閱讀 Twilio[https://www.twilio.com/docs/security][twilio_docs_security]安全準則。

有關 Twilio 的其他資訊，[https://www.twilio.com/docs][twilio_docs]請參閱。

## <a name="see-also"></a>另請參閱
* [如何在 Java 中透過 Twilio 使用語音和簡訊功能][howto_twilio_voice_sms_java]
* [新增憑證至 Java CA 憑證存放區][add_ca_cert]

[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_java_github]: https://github.com/twilio/twilio-java
[twimlet_message_url]: https://twimlets.com/message
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[azure_java_eclipse_hello_world]: https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app 
[howto_twilio_voice_sms_java]: partner-twilio-java-how-to-use-voice-sms.md
[howto_blob_storage_java]: https://www.windowsazure.com/develop/java/how-to-guides/blob-storage/
[howto_sql_azure_java]: https://msdn.microsoft.com/library/windowsazure/hh749029.aspx
[azure_runtime_jsp]: https://msdn.microsoft.com/library/windowsazure/hh690948.aspx
[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say
[twilio_java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg
[twilio_java_response]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg

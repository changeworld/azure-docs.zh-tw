---
title: Azure 通訊服務通話用戶端程式庫概觀
titleSuffix: An Azure Communication Services concept document
description: 提供通話用戶端程式庫的概觀。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e0fff67d6998e55854d4dc3c8db261bc4499f0be
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936051"
---
# <a name="calling-client-library-overview"></a>通話用戶端程式庫概觀

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

通話用戶端程式庫有兩個不同的系列，分別適用於「用戶端」和「服務」。 目前可用的用戶端程式庫適用於終端使用者體驗：網站和原生應用程式。

服務用戶端程式庫尚無法使用，並提供對未經處理語音和視訊資料平面的存取，適用於與 Bot 和其他服務整合。

## <a name="calling-client-library-capabilities"></a>通話用戶端程式庫功能

下列清單是目前 Azure 通訊服務通話用戶端程式庫中可用的一組功能。

| 功能群組 | 功能                                                                                                          | JS  | Java (Android) | Objective-C (iOS) 
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | ---  | -------------- | -------------
| 核心功能 | 在兩個使用者之間進行一對一通話                                                                           | ✔️   | ✔️            | ✔️  
|                   | 與兩個以上使用者 (最多 350 個使用者) 進行群組通話                                                       | ✔️   | ✔️            | ✔️ 
|                   | 將兩個使用者的一對一通話提升為兩個以上使用者的群組通話                                 | ✔️   | ✔️            | ✔️ 
|                   | 在群組通話開始之後加入                                                                              | ✔️   | ✔️            | ✔️ 
|                   | 邀請另一個 VoIP 參與者加入進行中的群組通話                                                       | ✔️   | ✔️            | ✔️
|                   | 開啟/關閉您的影片                                                         | ✔️   | ✔️            | ✔️ 
|                   | 靜音/取消靜音麥克風                                                                                                     | ✔️   | ✔️            | ✔️         
|                   | 在相機之間切換                                                                                              | ✔️   | ✔️            | ✔️           
|                   | 本機保留/取消保留                                                                                                  | ✔️   | ✔️            | ✔️           
|                   | 作用中喇叭                                                                                                      | ✔️   | ✔️            | ✔️           
|                   | 選擇通話的喇叭                                                                                            | ✔️   | ✔️            | ✔️           
|                   | 選擇通話的麥克風                                                                                         | ✔️   | ✔️            | ✔️           
|                   | 顯示參與者的狀態<br/>*閒置、接通通知、正在連線、已連線、保留、在大廳中，已中斷連線*         | ✔️   | ✔️            | ✔️           
|                   | 顯示通話的狀態<br/>*接通通知、連入、正在連線、鈴響中、已連線、保留、正在中斷連線、已中斷連線* | ✔️   | ✔️            | ✔️           
|                   | 顯示參與者是否已靜音                                                                                      | ✔️   | ✔️            | ✔️           
|                   | 顯示參與者離開通話的原因                                                                       | ✔️   | ✔️            | ✔️     
| 螢幕畫面分享    | 從應用程式內共用整個螢幕畫面                                                                 | ✔️   | ❌            | ❌           
|                   | 共用特定應用程式 (從執行中應用程式的清單)                                                | ✔️   | ❌            | ❌           
|                   | 從開啟的索引標籤清單共用 Web 瀏覽器索引標籤                                                                  | ✔️   | ❌            | ❌           
|                   | 參與者可以檢視遠端螢幕畫面分享                                                                            | ✔️   | ✔️            | ✔️         
| 名冊            | 列出參與者                                                                                                   | ✔️   | ✔️            | ✔️           
|                   | 移除參與者                                                                                                | ✔️   | ✔️            | ✔️         
| PSTN              | 與 PSTN 參與者進行一對一通話                                                                     | ✔️   | ✔️            | ✔️   
|                   | 與 PSTN 參與者進行群組通話                                                                           | ✔️   | ✔️            | ✔️
|                   | 將與 PSTN 參與者的一對一通話提升為群組通話                                                 | ✔️   | ✔️            | ✔️
|                   | 以 PSTN 參與者的身分從群組通話撥出                                                                    | ✔️   | ✔️            | ✔️   
| 一般           | 使用音訊測試服務測試您的麥克風、喇叭和相機 (可以透過呼叫 8:echo123 取得)                   |  ✔️  | ✔️            | ✔️   

## <a name="javascript-calling-client-library-support-by-os-and-browser"></a>JAVAscript 透過 OS 和瀏覽器呼叫用戶端程式庫支援

下表顯示目前可用的一組受支援瀏覽器和版本。

|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    | iPad OS|
| -------------------------------- | ---------------- | -------------- | ------- | ------ | ------ | ------ | -------|
| **通話用戶端程式庫** | Chrome*，新的 Edge | Chrome *，Safari** | Chrome*  | Chrome* | Chrome* | Safari** | Safari** |


*請注意，除了先前的兩個版本之外，也支援最新版本的 Chrome。<br/>

**請注意，支援 Safari 版本 13.1+。 尚不支援 Safari macOS 的傳出視訊，但是在 iOS 上支援。 只有桌面 iOS 才支援傳出螢幕畫面分享。 目前無法在 Safari 上使用 1:1 和群組通話。

## <a name="calling-client---browser-security-model"></a>呼叫用戶端 - 瀏覽器安全性模型

### <a name="user-webrtc-over-https"></a>透過 HTTPS 的使用者 WebRTC

WebRTC API (例如 `getUserMedia` ) 需要透過 HTTPS 提供呼叫這些 API 的應用程式。

您可以使用 `http://localhost` 進行本機開發。

### <a name="embed-the-communication-services-calling-sdk-in-an-iframe"></a>在 iframe 中內嵌通訊服務呼叫 SDK

許多瀏覽器已採用新的[權限原則 (也稱為功能原則)](https://www.w3.org/TR/permissions-policy-1/#iframe-allow-attribute)。 此原則會控制應用程式如何透過跨原始 iframe 元素來存取裝置的相機和麥克風，進而影響呼叫案例。

如果要使用 iframe 來裝載來自不同網域的部分應用程式，必須將具有正確值的 `allow` 屬性新增至您的 iframe。

例如，此 iframe 允許相機和麥克風的存取：

```html
<iframe allow="camera *; microphone *">
```

## <a name="calling-client-library-streaming-support"></a>通話用戶端程式庫串流支援
通訊服務的通話用戶端程式庫支援下列串流設定：

|           |Web | Android/iOS|
|-----------|----|------------|
|**可以同時傳送的傳出串流數目** |1 段影片 + 1 個螢幕共用 | 1 段影片 + 1 個螢幕共用|
|**可以同時轉譯的傳入串流數目** |1 段影片 + 1 個螢幕共用| 6 段影片 + 1 個螢幕共用 |


## <a name="next-steps"></a>下一步

> [!div class="nextstepaction"]
> [開始使用通話](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

如需詳細資訊，請參閱下列文章：
- 熟悉一般[通話流程](../call-flows.md)
- 深入了解[通話類型](../voice-video-calling/about-call-types.md)
- [規劃您的 PSTN 解決方案](../telephony-sms/plan-solution.md)

---
title: Azure 通訊服務通話用戶端程式庫概觀
titleSuffix: An Azure Communication Services concept document
description: 提供通話用戶端程式庫的概觀。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 6021bb4fa5d30de423ab56bfb5fdea6af2665a4a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91334475"
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

## <a name="calling-client-library-browser-support"></a>通話用戶端程式庫瀏覽器支援

下表顯示目前可用的一組受支援瀏覽器和版本。

|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    |
| -------------------------------- | ---------------- | -------------- | ------- | ------ | ------ | ------ |
| **通話用戶端程式庫** | Chrome*，新的 Edge | Chrome *，Safari** | Chrome*  | Chrome* | Chrome* | Safari** |


*請注意，除了先前的兩個版本之外，也支援最新版本的 Chrome。<br/>

**請注意，支援 Safari 版本 13.1+。 尚不支援 Safari macOS 的傳出視訊，但是在 iOS 上支援。 只有桌面 iOS 才支援傳出螢幕畫面分享。

## <a name="next-steps"></a>下一步

> [!div class="nextstepaction"]
> [開始使用通話](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

如需詳細資訊，請參閱下列文章：
- 熟悉一般[通話流程](../call-flows.md)
- 深入了解[通話類型](../voice-video-calling/about-call-types.md)
- [規劃您的 PSTN 解決方案](../telephony-sms/plan-solution.md)

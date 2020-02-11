---
title: Azure 媒體服務 v3 概觀
titleSuffix: Azure Media Services
description: Azure 媒體服務 v3 的高階概觀，其中包含快速入門、教學課程和程式碼範例的連結。
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: Azure 媒體服務, 串流, 廣播, 即時, 離線
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 02/03/2020
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: 559050d11b6b85a9842c679b94e0e5b2fd5dd0f8
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987708"
---
# <a name="azure-media-services-v3-overview"></a>Azure 媒體服務 v3 概觀

Azure 媒體服務是雲端式平台，可讓您建置的解決方案擁有廣播品質的視訊串流、增強協助工具和散佈、分析內容等等。 不論您是應用程式開發人員、話務中心、政府機構，還是娛樂公司，媒體服務都能協助您建立應用程式，為時下最熱門的行動裝置和瀏覽器上的廣大愛用者，提供優異品質的媒體體驗。

媒體服務 v3 SDK 是以[媒體服務 v3 OpenAPI 規格 (Swagger)](https://aka.ms/ams-v3-rest-sdk) \(英文\) 為基礎。

> [!NOTE]
> 目前您無法使用 Azure 入口網站管理 v3 資源。 請使用 [REST API](https://aka.ms/ams-v3-rest-ref)、[CLI](https://aka.ms/ams-v3-cli-ref) 或其中一個支援的 [SDK](media-services-apis-overview.md#sdks)。

## <a name="compliance-privacy-and-security"></a>合規性、隱私權和安全性
 
重要提醒是，在使用 Azure 媒體服務時，您必須遵守所有適用的法律，且您不得以違反他人權利或可能會對他人有害的方式使用 Azure 媒體服務或任何其他 Azure 服務。 

將任何影片/影像 (包括任何生物特徵辨識資料) 上傳至媒體服務以進行處理和儲存之前，您必須 (1) 擁有影片/影像的所有適當權限；(2) 向影片中的個人徵得所有必要的同意，允許在媒體服務中使用和儲存其生物特徵辨識資料。 

如需了解合規性、隱私權，以及 Microsoft 如何保護您在 Azure 媒體服務中的資料，請造訪 Azure [信任中心](https://www.microsoft.com/trust-center/?rtc=1)。 如需 Microsoft 的隱私權義務和您的資料處理方式，請參閱 Microsoft 的 [隱私權聲明](https://privacy.microsoft.com/PrivacyStatement)、[線上服務條款](https://www.microsoft.com/licensing/product-licensing/products) ("OST") 和 [資料處理增補](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA")。 客戶資料保留和刪除的相關資訊可在 DPA 中取得。 一旦使用 Azure 媒體服務，即表示您同意受到 OST、DPA 和隱私權聲明的規範。

## <a name="what-can-i-do-with-media-services"></a>我可以如何運用媒體服務？

媒體服務可讓您在雲端建置各種不同的媒體工作流程。 您可以使用媒體服務執行的一些範例包括：

* 以各種格式傳遞影片，使它們能在多種瀏覽器和裝置上播放。 不論是隨選播放或是即時串流傳遞至各種用戶端 (行動裝置、電視、電腦等)，視訊和音訊內容都必須經過適當地編碼和封裝。 若要了解如何傳遞和串流這類內容，請參閱[快速入門：編碼和串流檔案](stream-files-dotnet-quickstart.md)。
* 將即時體育賽事串流至廣大的線上觀眾，例如足球、棒球、大專院校和高中體育競賽等等。
* 廣播公眾會議和活動，例如市政府、市議會會議，及立法機構。
* 分析錄製的視訊或音訊內容。 例如，為了達到更高的客戶滿意度，組織可以擷取語音轉換文字，並建置搜尋索引和儀表板。 接著便可以從一般客訴、客訴來源及其他相關資料中獲取情報。
* 建立訂閱影片服務，並在客戶需要限制對著作權所有作品的存取及使用時，串流受 DRM 保護的內容。
* 傳遞離線內容，以在飛機、火車和汽車上播放。 當客戶預期會從網路中斷連線時，他們可能需要將內容下載到手機或平板電腦上播放。
* 使用 Azure 媒體服務和 [Azure 認知服務 API](https://docs.microsoft.com/azure/#pivot=products&panel=ai) 的語音文字轉換、翻譯成多語言等功能，實作教育電子化學習影片平台。
* 搭配使用 Azure 媒體服務與 [Azure 認知服務 API](https://docs.microsoft.com/azure/#pivot=products&panel=ai) 在影片加入標題和字幕，以服務更廣大的觀眾 (例如聽障人士或想要以其他語言閱讀的人)。
* 啟用 Azure CDN 以達成大規模調整，進而妥善處理瞬間高負載 (例如，在產品上市活動開始時)。

## <a name="how-can-i-get-started-with-v3"></a>如何開始使用 v3？ 

了解如何使用媒體服務 v3 來編碼和封裝內容、串流隨選影片、即時廣播及分析您的影片。 教學課程、API 參考和其他文件會示範如何安全地提供可針對數百萬位使用者調整的隨選和即時視訊或音訊串流。

> [!TIP]
> 在開始開發之前，請檢閱：<br/>* [基本概念](concepts-overview.md) (包括重要概念，例如封裝、編碼和保護)<br/>* [使用媒體服務 v3 API 進行開發](media-services-apis-overview.md) (包括存取 API、命名慣例等的詳細資訊)

### <a name="sdks"></a>SDK

開始使用 [Azure 媒體服務 v3 用戶端 SDK](media-services-apis-overview.md#sdks) 進行開發。

### <a name="quickstarts"></a>快速入門  

快速入門會顯示第 1 天的基本指示，讓新客戶快速試用媒體服務。

* [串流影片檔案 - .NET](stream-files-dotnet-quickstart.md)
* [串流影片檔案 - CLI](stream-files-cli-quickstart.md)
* [串流影片檔案 - Node.js](stream-files-nodejs-quickstart.md)

### <a name="tutorials"></a>教學課程

教學課程會顯示某些常用媒體服務工作的案例式程序。

* [遠端檔案編碼和串流影片 - REST](stream-files-tutorial-with-rest.md)
* [已上傳檔案編碼和串流影片 - .NET](stream-files-tutorial-with-api.md)
* [即時串流 - .NET](stream-live-tutorial-with-api.md)
* [分析影片 - .NET](analyze-videos-tutorial-with-api.md)
* [AES-128 動態加密 - .NET](protect-with-aes128.md)

### <a name="samples"></a>範例

使用[此範例瀏覽器](https://docs.microsoft.com/samples/browse/?products=azure-media-services)瀏覽 Azure 媒體服務程式碼範例。

### <a name="how-to-guides"></a>操作指南

操作指南中包含示範如何完成工作的程式碼範例。 在本節中，您會發現許多範例。 以下提供其中一些範例：

* [建立帳戶 - CLI](create-account-cli-how-to.md)
* [存取 API - CLI](access-api-cli-how-to.md)
* [使用 HTTPS 即作業輸入編碼 - .NET](job-input-from-http-how-to.md)  
* [監視事件 - 入口網站](monitor-events-portal-how-to.md)
* [透過多重 DRM 進行動態加密 - .NET](protect-with-drm.md) 
* [如何對自訂轉換進行編碼 - CLI](custom-preset-cli-howto.md)

## <a name="ask-questions-give-feedback-get-updates"></a>提出問題、提供意見反應、取得更新

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="next-steps"></a>後續步驟

[了解基本概念](concepts-overview.md)


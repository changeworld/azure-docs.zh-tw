---
title: 使用 Azure 媒體服務保護您的內容 | Microsoft Docs
description: 本文概述了 Azure 媒體服務 v2 的內容保護。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 81bc00e1-dcda-4d69-b9ab-8768b793422b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 88e0e1c18722fd86e79fc1fa7722b59b3cb8966a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460954"
---
# <a name="content-protection-overview"></a>內容保護概觀 

> [!NOTE]
> 媒體服務 v2 不會再新增任何新的特性或功能。 <br/>查看最新版本，[媒體服務 v3](https://docs.microsoft.com/azure/media-services/latest/)。 此外，請參閱[從 v2 到 v3 的遷移指南](../latest/migrate-from-v2-to-v3.md)

您可以使用 Azure 媒體服務來保護媒體從離開電腦到進行儲存、處理和傳遞時的安全。 使用媒體服務，您就能傳遞利用進階加密標準 (AES-128) 或下列三個主要數位版權管理 (DRM) 系統中任一個所動態加密的即時與隨選內容：Microsoft PlayReady、Google Widevine 和 Apple FairPlay。 媒體服務也提供服務，可傳遞 AES 金鑰和 DRM (PlayReady、Widevine 和 FairPlay) 授權給授權用戶端。 

下圖說明媒體服務內容保護工作流程： 

![利用 PlayReady 保護](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

本文說明可了解如何使用媒體服務進行內容保護的相關概念與術語。 本文也會提供討論如何保護內容的文章連結。 

## <a name="dynamic-encryption"></a>動態加密

您可以使用媒體服務，來傳遞藉由使用 PlayReady、Widevine 或 FairPlay 並透過 AES 未加密金鑰或 DRM 加密所動態加密的內容。 如果內容使用 AES 清除金鑰進行加密，並且通過 HTTPS 發送，則在到達用戶端之前不會清除內容。 

每一種加密方法都支援下列串流處理通訊協定：
 
- AES：MPEG DASH、 Smooth Streaming 和 HLS
- PlayReady：MPEG DASH、 Smooth Streaming 和 HLS
- Widevine：MPEG DASH
- FairPlay：HLS

不支援在漸進式下載上加密。 

若要加密資產，您需要建立加密內容金鑰與您資產的關聯，同時也要設定金鑰的授權原則。 內容金鑰可由您指定或由媒體服務自動產生。

您也需要設定資產的傳遞原則。 如果您想要串流處理儲存體加密的資產，請務必透過設定資產傳遞原則來指定資產傳遞方式。

播放程式要求串流時，媒體服務便會使用 AES 清除金鑰或 DRM 加密，使用指定的金鑰動態加密您的內容。 為了將串流解密，播放程式會向媒體服務金鑰傳遞服務要求金鑰。 為了決定使用者是否有權取得金鑰，服務會評估為金鑰指定的授權原則。

## <a name="aes-128-clear-key-vs-drm"></a>AES-128 清除鍵與 DRM
客戶通常不知道他們應該使用 AES 加密，還是 DRM 系統。 這兩個系統的主要差異是，使用 AES 加密時，系統會以未加密格式 (「純文字」) 將內容金鑰傳輸至用戶端。 因此，就能在用戶端的網路追蹤中，以純文字形式檢視用來加密內容的金鑰。 AES-128 未加密金鑰加密適用於檢視者為受信任合作對象的使用案例 (例如，將在公司內部散發的公司影片加密，以供員工檢視)。

相較於 AES-128 未加密金鑰加密，PlayReady、Widevine 及 FairPlay 全都會提供較高層級的加密。 內容金鑰會以加密格式傳輸。 此外，解密會在安全環境中，惡意使用者更難攻擊的作業系統層級上進行處理。 針對檢視者可能不是受信任的合作對象，而且您需要最高層級安全性的使用案例，建議使用 DRM。

## <a name="storage-encryption"></a>儲存體加密
您可以藉由儲存體加密，使用 AES 256 位元加密對您的明文內容進行本機加密。 然後，您可以將它上傳到 Azure 儲存體，以便以靜止狀態加密儲存。 以儲存體加密保護的資產會自動解除加密並在編碼前放置在加密的檔案系統中。 在以新輸出資產的形式上傳回去之前，系統會選擇性地重新加密這些資產。 儲存體加密的主要使用案例是，讓您可以使用強式加密來保護磁碟中靜止的高品質輸入媒體檔。

若要傳遞儲存體加密資產，您必須設定資產的傳遞原則，讓媒體服務知道您的內容傳遞方式。 串流處理資產之前，串流伺服器會使用指定的傳遞原則 (例如，AES、一般加密或不加密) 來解密並串流處理您的內容。

## <a name="types-of-encryption"></a>加密的類型
Playready 和 Widevine 會利用一般加密 (AES CTR 模式)。 FairPlay 會利用 AES CBC 模式加密。 AES-128 未加密金鑰加密會利用信封加密。

## <a name="licenses-and-keys-delivery-service"></a>授權和金鑰傳遞服務
媒體服務提供一種金鑰傳遞服務，可將 DRM (PlayReady、Widevine 與 FairPlay) 授權和 AES 金鑰傳遞給授權用戶端。 若要設定您授權和金鑰的授權和驗證原則，才能使用 [Azure 入口網站](media-services-portal-protect-content.md)、REST API 或 Media Services SDK for .NET。

## <a name="control-content-access"></a>控制內容存取
您可以設定內容金鑰授權原則來控制可存取內容的人員。 內容金鑰授權原則支援 open 或權杖限制。

### <a name="open-authorization"></a>Open 授權
利用 open 授權原則，可將內容金鑰傳送給任何用戶端 (無限制)。

### <a name="token-authorization"></a>權杖授權
使用權杖限制的授權原則時，系統只會將內容金鑰傳送給可在金鑰/授權要求中提供有效 JSON Web 權杖 (JWT) 或簡單 Web 權杖 (SWT) 的用戶端。 這個權杖必須由 Security Token Service (STS) 來核發。 您可以使用 Azure Active Directory 作為 STS，或部署自訂 STS。 STS 必須設定為建立使用指定的索引鍵和問題宣告您在權杖限制組態中指定簽署的權杖。 如果權杖有效，且權杖中的宣告符合針對金鑰/授權所設定的宣告，則媒體服務金鑰傳遞服務會將所要求的金鑰/授權傳回給用戶端。

設定權杖限制的原則時，您必須指定主要驗證金鑰、簽發者和對象參數。 主要驗證金鑰包含簽署權杖用的金鑰。 簽發者為發行權杖的安全性權杖服務。 對象 (有時稱為「範圍」) 描述權杖或權杖獲授權存取之資源的用途。 媒體服務金鑰傳遞服務會驗證權杖中的這些值符合在範本中的值。

### <a name="token-replay-prevention"></a>權杖重播預防

*權杖重播防止*功能允許媒體服務客戶對同一權杖可用於請求金鑰或許可證的次數設置限制。 客戶可以在權杖中添加類型`urn:microsoft:azure:mediaservices:maxuses`聲明，其中值是權杖可用於獲取許可證或金鑰的次數。 所有對金鑰傳遞具有相同權杖的後續請求都將返回未經授權的回應。 瞭解如何在[DRM 示例中](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601)添加聲明。
 
#### <a name="considerations"></a>考量

* 客戶必須控制權杖生成。 聲明需要放在權杖本身中。
* 使用此功能時，過期時間超過一小時，並且使用未經授權的回應拒絕具有過期時間超過一小時的權杖的請求。
* 權杖由其簽名唯一標識。 對負載的任何更改（例如，更新到到期時間或聲明）都會更改權杖的簽名，它將算作金鑰交付以前未遇到的新權杖。
* 如果權杖已超過客戶設置的值，`maxuses`則重播將失敗。
* 此功能可用於所有現有的受保護內容（只需更改頒發的權杖）。
* 此功能適用于 JWT 和 SWT。

## <a name="streaming-urls"></a>串流 URL
如果使用一個以上 DRM 來加密您的資產，請使用串流 URL 中的加密標籤：(format='m3u8-aapl', encryption='xxx')。

您必須考量下列事項：

* 無法指定一個以上的加密類型。
* 如果只有一個加密套用到資產，則無須在 URL 中指定加密類型。
* 加密類型不區分大小寫。
* 可以指定下列加密類型︰

  * **cenc**︰適用於 PlayReady 或 Widevine (一般加密)
  * **cbcs-aapl**：適用於 FairPlay (AES CBC 加密)
  * **cbc**：適用於 AES 信封加密

## <a name="additional-notes"></a>其他注意事項

* Widevine 是 Google Inc. 所提供的服務，並受到 Google Inc. 的服務條款和隱私權原則所約束。

## <a name="next-steps"></a>後續步驟
下列文章描述可協助您開始使用內容保護的後續步驟：

* [使用儲存體加密保護](media-services-rest-storage-encryption.md)
* [使用 AES 加密保護](media-services-protect-with-aes128.md)
* [使用播放就緒和/或寬維文進行保護](media-services-protect-with-playready-widevine.md)
* [使用 FairPlay 保護](media-services-protect-hls-with-FairPlay.md)

## <a name="related-links"></a>相關連結

* [JWT 權杖驗證](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
* [整合 Azure 媒體服務 OWIN MVC 型應用程式與 Azure Active Directory 並根據 JWT 宣告限制內容金鑰傳遞](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png

---
title: 成為內部部署編碼器合作夥伴-Azure 媒體服務
description: 成為具有 Azure 媒體服務的內部部署編碼器合作夥伴。
services: media-services
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 03/02/2020
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 6b00e430f960195e1badd2a73f9291997b94c833
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2020
ms.locfileid: "78252970"
---
# <a name="become-an-on-premises-encoder-partner"></a>成為內部部署編碼器合作夥伴

身為 Azure 媒體服務內部部署編碼器合作夥伴，媒體服務將會向企業客戶建議您的編碼器，以協助推廣您的產品。 若要成為內部部署編碼器合作夥伴，您必須向媒體服務驗證您內部部署編碼器的相容性。 若要這樣做，請完成下列驗證。

### <a name="pass-through-live-event-verification"></a>傳遞即時事件驗證

1. 在您的媒體服務帳戶中，確認**串流端點**正在執行。 
2. 建立並啟動**傳遞**即時事件。 <br/> 如需詳細資訊，請參閱[實況活動狀態和計費](live-event-states-billing.md)。
3. 取得內嵌 URL 並設定您的內部部署編碼器使用該 URL，以將多位元速率即時資料流傳送到媒體服務。
4. 取得預覽 URL 並使用它來確認實際上已收到來自編碼器的輸入。
5. 建立新的 [資產] 物件。
6. 建立 [即時輸出] 並使用您建立的資產名稱。
7. 使用內建的**串流原則**類型來建立**串流定位器**。
8. 列出**串流定位器**上的路徑，以取回要使用的 URL。
9. 取得您想要串流的來源**串流端點**主機名稱。
10. 結合步驟 8 的 URL 和步驟 9 的主機名稱，即可取得完整的 URL。
11. 將您的即時編碼器執行約 10 分鐘的時間。
12. 停止即時事件。 
13. 使用 [Azure 媒體播放器](https://aka.ms/azuremediaplayer)等播放程式觀看已封存的資產)，以確保所有品質層級的播放沒有可見問題。 或者，在即時工作階段期間透過預覽 URL 觀看和驗證。
14. 記錄資產識別碼、即時封存的已發行串流 URL，以及來自您即時編碼器的所使用設定和版本。
15. 於建立每個範例之後，重設即時事件狀態。
16. 針對您編碼器所支援的所有設定 (包含及不包含廣告訊號、字幕，或不同的編碼速度) 重複步驟 5 到步驟 15。

### <a name="live-encoding-live-event-verification"></a>即時編碼即時事件驗證

1. 在您的媒體服務帳戶中，確認**串流端點**正在執行。 
2. 建立並啟動**即時編碼**即時事件。 <br/> 如需詳細資訊，請參閱[實況活動狀態和計費](live-event-states-billing.md)。
3. 取得內嵌 URL 並設定您的編碼器將單一位元速率即時資料流推送到媒體服務。
4. 取得預覽 URL 並使用它來確認實際上已收到來自編碼器的輸入。
5. 建立新的 [資產] 物件。
6. 建立 [即時輸出] 並使用您建立的資產名稱。
7. 使用內建的**串流原則**類型來建立**串流定位器**。
8. 列出**串流定位器**上的路徑，以取回要使用的 URL。
9. 取得您想要串流的來源**串流端點**主機名稱。
10. 結合步驟 8 的 URL 和步驟 9 的主機名稱，即可取得完整的 URL。
11. 將您的即時編碼器執行約 10 分鐘的時間。
12. 停止即時事件。
13. 使用 [Azure 媒體播放器](https://aka.ms/azuremediaplayer)等播放程式觀看已封存的資產)，以確保所有品質層級的播放沒有可見問題。 或者，在即時工作階段期間透過預覽 URL 觀看和驗證。
14. 記錄資產識別碼、即時封存的已發行串流 URL，以及來自您即時編碼器的所使用設定和版本。
15. 於建立每個範例之後，重設即時事件狀態。
16. 針對您編碼器所支援的所有設定 (包含及不包含廣告訊號、字幕，或不同的編碼速度) 重複步驟 5 到步驟 15。

### <a name="longevity-verification"></a>使用壽命驗證

除了步驟 11 外，遵循與[傳遞即時事件驗證](#pass-through-live-event-verification)中相同的其他步驟。 <br/>讓您的即時編碼器執行一週或更久，而不是 10 分鐘。 使用 [Azure 媒體播放器](https://aka.ms/azuremediaplayer)等播放程式不時觀看即時串流 (或已封存的資產)，以確保播放沒有可見問題。

### <a name="email-your-recorded-settings"></a>用電子郵件傳送記錄的設定

最後，以電子郵件將記錄的設定和即時封存參數傳送到 amshelp@microsoft.com 的 Azure 媒體服務，做為所有自我驗證檢查已通過的通知。 此外，包含任何後續動作的連絡資訊。 若有任何關於此程序的問題，請連絡 Azure 媒體服務團隊。

## <a name="see-also"></a>另請參閱

[建議的內部部署即時編碼器](recommended-on-premises-live-encoders.md)

## <a name="next-steps"></a>後續步驟

[使用媒體服務 v3 進行即時串流](live-streaming-overview.md)

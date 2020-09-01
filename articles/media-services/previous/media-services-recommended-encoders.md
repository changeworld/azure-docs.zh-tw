---
title: 了解 Azure 媒體服務所建議的編碼器 | Microsoft Docs
description: 本文列出 Azure 媒體服務所建議的內部部署編碼器。
services: media-services
keywords: encoding;encoders;media;編碼;編碼器;媒體
author: dbgeorge
manager: johndeu
ms.author: johndeu
ms.date: 03/20/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 91ccc4395bf328d14698e7fe5db634a773197660
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89259518"
---
# <a name="recommended-on-premises-encoders"></a>建議的內部部署編碼器

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

使用 Azure 媒體服務進行即時串流時，您可以指定想讓通道接收輸入資料流的方式。 如果您選擇使用內部部署編碼器搭配即時編碼通道，您的編碼器應該將高品質的單一位元速率串流推送為輸出。 如果您選擇使用具有通過通道的內部部署編碼器，您的編碼器應該將多位元率串流作為輸出，以符合所有所需的輸出品質。 如需詳細資訊，請參閱 [使用內部部署編碼器進行即時串流](media-services-live-streaming-with-onprem-encoders.md)。

## <a name="encoder-requirements"></a>編碼器需求

使用 HTTPS 或 RTMPS 通訊協定時，編碼器必須支援 TLS 1.2。

## <a name="live-encoders-that-output-rtmp"></a>輸出 RTMP 的即時編碼器 

Azure 媒體服務建議使用下列其中一種具有 RTMP 作為輸出的即時編碼器：

- Adobe Flash Media Live Encoder 3.2
- Haivision Makito X HEVC
- Haivision KB
- Telestream Wirecast (版本13.0.2 或更高版本，因為 TLS 1.2 需求) 

  使用 RTMPS 通訊協定時，編碼器必須支援 TLS 1.2。
- Teradek Slice 756
- OBS Studio
- VMIX
- xStream
- Switcher Studio (iOS)

## <a name="live-encoders-that-output-fragmented-mp4"></a>輸出分散式 MP4 的即時編碼器 

Azure 媒體服務建議使用下列其中一種具有多位元速率分散式 MP4 (Smooth Streaming) 作為輸出的即時編碼器：

- Media Excel Hero Live 和 Hero 4K (UHD/HEVC)
- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live (version 2.14.15 和更高版本，因為 TLS 1.2 需求) 

  使用 HTTPS 通訊協定時，編碼器必須支援 TLS 1.2。
- Envivio 4Caster C4 Gen III
- Imagine Communications Selenio MCP3

> [!NOTE]
> 即時編碼器可傳送單一位元速率資料流至傳遞通道，但不建議使用此設定，因為它不允序針對用戶端使用彈性位元速率串流。

## <a name="how-to-become-an-on-premises-encoder-partner"></a>如何成為內部部署編碼器合作夥伴

作為 Azure 媒體服務的內部部署編碼器合作夥伴，媒體服務會向企業客戶建議您的編碼器，以提升您的產品。 若要成為內部部署編碼器合作夥伴，您必須使用媒體服務確認內部部署編碼器的相容性。 若要這樣做，請完成下列驗證：

傳遞通道驗證
1. 建立或造訪您的 Azure 媒體服務帳戶
2. 建立並啟動**傳遞**通道
3. 設定您的編碼器以推送多位元速率即時資料流。
4. 建立已發行的即時事件
5. 將您的即時編碼器執行約 10 分鐘的時間
6. 停止即時事件
7. 建立、啟動串流端點、使用 [Azure 媒體播放機](https://aka.ms/azuremediaplayer) 的播放程式來觀看已封存的資產，以確保所有品質層 (級的播放沒有可見的問題，或是在步驟6之前的即時會話期間透過預覽 URL 觀看和驗證) 
8. 記錄資產識別碼、即時封存的已發行串流 URL，以及來自您即時編碼器的所使用設定和版本
9. 於建立每個範例之後，重設通道狀態
10. 針對您編碼器所支援的所有設定 (包含及不包含廣告訊號/字幕/不同的編碼速度) 重複步驟 3 到步驟 9

即時編碼通道驗證
1. 建立或造訪您的 Azure 媒體服務帳戶
2. 建立及啟動**即時編碼**通道
3. 設定您的編碼器以推送單一位元速率即時資料流。
4. 建立已發行的即時事件
5. 將您的即時編碼器執行約 10 分鐘的時間
6. 停止即時事件
7. 建立、啟動串流端點、使用 [Azure 媒體播放機](https://aka.ms/azuremediaplayer) 的播放程式來觀看已封存的資產，以確保所有品質層 (級的播放沒有可見的問題，或是在步驟6之前的即時會話期間透過預覽 URL 觀看和驗證) 
8. 記錄資產識別碼、即時封存的已發行串流 URL，以及來自您即時編碼器的所使用設定和版本
9. 於建立每個範例之後，重設通道狀態
10. 針對您編碼器所支援的所有設定 (包含及不包含廣告訊號/字幕/各種編碼速度) 重複步驟 3 到步驟 9

使用壽命驗證
1. 建立或造訪您的 Azure 媒體服務帳戶
2. 建立並啟動**傳遞**通道
3. 設定您的編碼器以推送多位元速率即時資料流。
4. 建立已發行的即時事件
5. 將您的即時編碼器執行一週或更久的時間
6. 使用播放程式（例如 [Azure 媒體播放機](https://aka.ms/azuremediaplayer) ）觀看即時串流 (或封存的資產) ，以確保播放沒有可見的問題
7. 停止即時事件
8. 記錄資產識別碼、即時封存的已發行串流 URL，以及來自您即時編碼器的所使用設定和版本

最後，將您已記錄的設定和即時封存參數，以電子郵件傳送至媒體服務 (amsstreaming@microsoft.com)。 媒體服務收到之後，便會針對您即時編碼器的範例執行驗證測試。 若有任何關於此程序的問題，請連絡媒體服務。

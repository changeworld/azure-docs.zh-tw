---
title: 了解 Azure 媒體服務所建議的編碼器 | Microsoft Docs
description: 本文列出了 Azure 媒體服務推薦的本地編碼器。
services: media-services
keywords: encoding;encoders;media;編碼;編碼器;媒體
author: dbgeorge
manager: johndeu
ms.author: johndeu
ms.date: 03/20/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 89b01a3fb066f181f5ec54b481b71feaa7a6ae08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77131394"
---
# <a name="recommended-on-premises-encoders"></a>建議的內部部署編碼器

使用 Azure 媒體服務進行即時串流時，您可以指定想讓通道接收輸入資料流的方式。 如果選擇使用具有即時編碼通道的本地編碼器，則編碼器應將高品質的單位元速率流推送為輸出。 如果選擇使用具有通道的本地編碼器，則編碼器應將多位元率流作為輸出，並具有所有所需的輸出品質。 有關詳細資訊，請參閱[使用本地編碼器進行即時流式處理](media-services-live-streaming-with-onprem-encoders.md)。

## <a name="encoder-requirements"></a>編碼器要求

使用 HTTPS 或 RTMPS 協定時，編碼器必須支援 TLS 1.2。

## <a name="live-encoders-that-output-rtmp"></a>輸出 RTMP 的即時編碼器 

Azure 媒體服務建議使用下列其中一種具有 RTMP 作為輸出的即時編碼器：

- Adobe Flash Media Live Encoder 3.2
- Haivision Makito X HEVC
- Haivision KB
- 由於 TLS 1.2 要求，遠端直播有線廣播（版本 13.0.2 或更高版本）

  使用 RTMPS 協定時，編碼器必須支援 TLS 1.2。
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- OBS Studio
- VMIX
- xStream
- Switcher Studio (iOS)

## <a name="live-encoders-that-output-fragmented-mp4"></a>輸出分散式 MP4 的即時編碼器 

Azure 媒體服務建議使用下列其中一種具有多位元速率分散式 MP4 (Smooth Streaming) 作為輸出的即時編碼器：

- Media Excel Hero Live 和 Hero 4K (UHD/HEVC)
- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- 元素即時（版本 2.14.15 及以上，由於 TLS 1.2 要求）

  使用 HTTPS 協定時，編碼器必須支援 TLS 1.2。
- Envivio 4Caster C4 Gen III
- Imagine Communications Selenio MCP3

> [!NOTE]
> 即時編碼器可傳送單一位元速率資料流至傳遞通道，但不建議使用此設定，因為它不允序針對用戶端使用彈性位元速率串流。

## <a name="how-to-become-an-on-premises-encoder-partner"></a>如何成為本地編碼器合作夥伴

作為本地編碼器上的 Azure 媒體服務合作夥伴，媒體服務通過向企業客戶推薦編碼器來提升產品。 要成為本地編碼器合作夥伴，必須驗證內部本地編碼器與媒體服務的相容性。 若要這樣做，請完成下列驗證：

傳遞通道驗證
1. 建立或造訪您的 Azure 媒體服務帳戶
2. 建立並啟動**傳遞**通道
3. 設定您的編碼器以推送多位元速率即時資料流。
4. 建立已發行的即時事件
5. 將您的即時編碼器執行約 10 分鐘的時間
6. 停止即時事件
7. 創建、啟動流式處理終結點、使用 Azure[媒體播放機等](https://aka.ms/azuremediaplayer)播放機監視存檔資產，以確保播放沒有所有品質級別的可見故障（或者，在步驟 6 之前的即時會話期間，通過預覽 URL 觀看和驗證）
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
7. 創建、啟動流式處理終結點、使用 Azure[媒體播放機等](https://aka.ms/azuremediaplayer)播放機監視存檔資產，以確保播放沒有所有品質級別的可見故障（或者，在步驟 6 之前的即時會話期間，通過預覽 URL 觀看和驗證）
8. 記錄資產識別碼、即時封存的已發行串流 URL，以及來自您即時編碼器的所使用設定和版本
9. 於建立每個範例之後，重設通道狀態
10. 針對您編碼器所支援的所有設定 (包含及不包含廣告訊號/字幕/各種編碼速度) 重複步驟 3 到步驟 9

使用壽命驗證
1. 建立或造訪您的 Azure 媒體服務帳戶
2. 建立並啟動**傳遞**通道
3. 設定您的編碼器以推送多位元速率即時資料流。
4. 建立已發行的即時事件
5. 將您的即時編碼器執行一週或更久的時間
6. 使用[Azure 媒體播放機](https://aka.ms/azuremediaplayer)等播放機不時觀看即時流式處理（或存檔資產），以確保播放沒有可見的故障
7. 停止即時事件
8. 記錄資產識別碼、即時封存的已發行串流 URL，以及來自您即時編碼器的所使用設定和版本

最後，將您已記錄的設定和即時封存參數，以電子郵件傳送至媒體服務 (amsstreaming@microsoft.com)。 媒體服務收到之後，便會針對您即時編碼器的範例執行驗證測試。 若有任何關於此程序的問題，請連絡媒體服務。

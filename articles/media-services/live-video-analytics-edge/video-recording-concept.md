---
title: 錄製影片-Azure
description: 在 CCTV 攝影機的影片管理系統內容中，影片錄製是指從攝影機捕獲影片並錄製以透過行動和瀏覽器應用程式進行觀看的流程。 影片錄製可分類為連續的影片錄製和以事件為基礎的影片錄製。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: c03472f577e67d5b2705a2ebe3f53e85e0849819
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84260191"
---
# <a name="video-recording"></a>影片錄製

在 CCTV 攝影機的影片管理系統內容中，影片錄製是指從攝影機捕獲影片並錄製以透過行動和瀏覽器應用程式進行觀看的流程。 影片錄製可分類為連續的影片錄製和以事件為基礎的影片錄製。 

## <a name="continuous-video-recording"></a>連續影片錄製  

持續錄製的影片 (CVR) 指的是持續錄製從影片來源中所捕捉到的所有影片的流程。 CVR 可確保所有的影片都可供使用， (由 [錄製原則](#recording-policy)) ，以在稍後的時間點進行分析及/或審核。

## <a name="event-based-video-recording"></a>以事件為基礎的影片錄製  

以事件為基礎的影片錄製 (EVR) 是指事件觸發的影片的錄製流程。 有問題的事件可能是因為視訊訊號本身的處理，或是來自獨立的來源 (例如，從大門感應器) 。 以事件為基礎的影片錄製 (EVR) 可能會節省儲存空間，但需要額外的元件來產生事件，並根據預先定義的原則) 觸發影片記錄 (。 換句話說，EVR 需要針對應觸發影片錄製的事件以及與影片 (錄製相關聯的原則（也稱為錄製原則) ）進行額外的決策。 原則的範例可能類似：記錄影片2分鐘，從事件時間之前的30秒開始。 有問題的事件可能是因為攝影機本身的影片處理所致。 例如，當偵測到在相機的區域內有興趣的預先設定區域內偵測到動作時，有數個攝影機支援產生動作偵測訊號事件。 也可以藉由在另一個裝置上處理影片來產生事件，並使用簡單的影像處理技術或精密的機器學習模型來分析影片。 

## <a name="choosing-recording-modes"></a>選擇錄製模式  

選擇是否要使用 CVR 或 EVR 取決於商務目標。 IoT Edge 上的即時影片分析提供 CVR 和 EVR 的平臺功能。 您可以在 [CVR](continuous-video-recording-concept.md) 和 [EVR](event-based-video-recording-concept.md) 案例文章中深入瞭解。

## <a name="recording-policy"></a>記錄原則  

記錄原則指的是在 EVR) 、記錄持續時間和錄製刪除的情況下，用來指示錄製開始/停止時間 (的原則。 記錄原則可讓您在儲存體成本與商務需求之間取得平衡。 CVR 和 EVR 之間的記錄原則不同。 若為 CVR，記錄原則會定義要儲存多少天的影片 (例如，過去7天的) 。 若為 EVR，記錄原則會定義錄製應該開始和結束的時間，以及影片的持續時間。 您可以在 [CVR](continuous-video-recording-concept.md) 和 [EVR](event-based-video-recording-concept.md) 案例文章中深入瞭解。

## <a name="next-steps"></a>接下來的步驟

* [偵測動作、將影片剪輯錄製到 Azure 媒體服務](detect-motion-record-video-clips-media-services-quickstart.md)
* [偵測動作、將影片剪輯記錄到邊緣裝置](detect-motion-record-video-clips-edge-devices-quickstart.md)


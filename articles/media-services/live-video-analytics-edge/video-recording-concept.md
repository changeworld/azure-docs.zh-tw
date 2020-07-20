---
title: 錄製影片-Azure
description: 在 CCTV 攝影機的影片管理系統內容中，影片錄製指的是從攝影機捕捉影片，並將其錄製以透過行動和瀏覽器應用程式進行觀看的流程。 影片錄製可以分類為連續的錄影和以事件為基礎的影片錄製。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: c03472f577e67d5b2705a2ebe3f53e85e0849819
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84260191"
---
# <a name="video-recording"></a>影片錄製

在 CCTV 攝影機的影片管理系統內容中，影片錄製指的是從攝影機捕捉影片，並將其錄製以透過行動和瀏覽器應用程式進行觀看的流程。 影片錄製可以分類為連續的錄影和以事件為基礎的影片錄製。 

## <a name="continuous-video-recording"></a>連續影片錄製  

持續錄影（CVR）指的是持續記錄從影片來源所捕捉到的所有影片。 CVR 可確保所有的影片都可供使用（由[錄製原則](#recording-policy)決定），以在稍後的時間點分析及/或進行審核。

## <a name="event-based-video-recording"></a>以事件為基礎的影片錄製  

以事件為基礎的影片錄製 (EVR) 是指事件觸發的影片的錄製流程。 有問題的事件可能源自于視訊訊號本身的處理，或是來自獨立的來源（例如，來自門感應器）。 以事件為基礎的影片錄影（EVR）可能會節省儲存空間，但它需要額外的元件來產生事件並觸發影片錄製（根據預先定義的原則）。 換句話說，EVR 需要對應該觸發影片錄製的事件以及與錄影錄影（也稱為錄音原則）相關聯的原則做出額外的決策。 原則的範例如下：從事件時間之前的30秒開始，錄製影片2分鐘。 有問題的事件可能源自相機本身的視頻處理。 例如，數個相機支援在相機的區域中，于相關的預先設定區域內偵測到動作時，產生動作偵測訊號事件。 您也可以在另一個裝置上處理影片，以從相機捕捉影片，並使用簡單的影像處理技術或複雜的機器學習模型來分析這些事件。 

## <a name="choosing-recording-modes"></a>選擇錄製模式  

選擇是否要使用 CVR 或 EVR，取決於商務目標。 IoT Edge 上的即時影片分析提供 CVR 和 EVR 的平臺功能。 您可以在[CVR](continuous-video-recording-concept.md)和[EVR](event-based-video-recording-concept.md)案例文章中深入瞭解。

## <a name="recording-policy"></a>記錄原則  

記錄原則指的是指示錄製開始/停止時間的原則（如果是 EVR）、錄製持續時間，以及刪除記錄。 記錄原則可讓您在儲存成本與業務需求之間取得平衡。 記錄原則在 CVR 與 EVR 之間有所不同。 針對 CVR，記錄原則會定義應該儲存的影片天數（例如，過去7天）。 針對 EVR，記錄原則會定義錄製的開始和結束時間，以及影片的持續期間。 您可以在[CVR](continuous-video-recording-concept.md)和[EVR](event-based-video-recording-concept.md)案例文章中深入瞭解。

## <a name="next-steps"></a>後續步驟

* [偵測動作、將影片剪輯錄製到 Azure 媒體服務](detect-motion-record-video-clips-media-services-quickstart.md)
* [偵測動作，將影片剪輯錄製到邊緣裝置](detect-motion-record-video-clips-edge-devices-quickstart.md)


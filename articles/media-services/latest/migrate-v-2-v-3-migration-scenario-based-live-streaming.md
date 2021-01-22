---
title: 媒體服務 v2 至 v3 遷移以案例為基礎的即時串流指引 |Microsoft Docs
description: 本文提供以即時串流案例為基礎的指導方針，可協助您將從 Azure 媒體服務 v2 遷移至 v3 的時間最小。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 89fcf85b20d11664d5d1caa3fbe142fa5bbdbebc
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98690279"
---
# <a name="live-streaming-scenario-based-migration-guidance"></a>即時串流以案例為基礎的遷移指導方針

![遷移指南標誌](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![遷移步驟2](./media/migration-guide/steps-4.svg)

Azure 入口網站現在支援即時事件設定和管理。  建議您在測試 V2 至 V3 遷移時試用它。

## <a name="test-the-v3-live-event-workflow"></a>測試 V3 實況活動工作流程

> [!NOTE]
> 使用 v2 (所建立的通道和程式會對應至 v3 中的即時事件和即時輸出) 使用 V3 api 來管理。 本指引是在您可以停止現有 V2 通道時，于方便的時間切換至 V3 實況活動和即時輸出。 目前並不支援將持續執行的全天候即時通道順暢地遷移至新的 V3 實況活動。 因此，維護停機時間需要協調，以提供您的物件和業務的最佳便利性。

先測試以媒體服務傳遞實況活動的新方式，再將您的內容從 V2 移至 V3。 以下是要使用並考慮遷移的 V3 功能。

- 建立新的 v3 [實況活動](live-events-outputs-concept.md#live-events) 以進行編碼。 您可以啟用 [1080p 和720p 編碼](live-event-types-comparison.md#system-presets)預設值。
- 使用 [即時輸出](live-events-outputs-concept.md#live-outputs) 實體，而不是程式
- 建立 [串流定位器](streaming-locators-concept.md)。
- 請考慮您對 [HLS 和虛線](dynamic-packaging-overview.md) 即時串流的需求。
- 如果您需要快速啟動實況活動，請流覽新的 [待命模式](live-events-outputs-concept.md#standby-mode) 功能。
- 如果您想要在發生活動時轉譯實況活動，請探索新的 [即時](live-transcription.md) 轉譯功能。
- 如果您需要較長的串流期間，請在 v3 中建立全天候實況活動。
- 使用 [事件方格](monitor-events-portal-how-to.md) 來監視您的實況活動。

如需特定步驟，請參閱實況活動概念、教學課程和操作說明指南。

## <a name="live-events-concepts-tutorials-and-how-to-guides"></a>實況活動概念、教學課程和操作指南

### <a name="concepts"></a>概念

- [使用 Azure 媒體服務 v3 進行即時串流](live-streaming-overview.md)
- [媒體服務中的即時活動和即時輸出](live-events-outputs-concept.md)
- [已驗證內部部署即時串流編碼器](recommended-on-premises-live-encoders.md)
- [使用時間變化和即時輸出來建立隨選影片播放](live-event-cloud-dvr.md)
- [即時轉譯 (預覽) ](live-transcription.md)
- [實況活動類型比較](live-event-types-comparison.md)
- [實況活動狀態和計費](live-event-states-billing.md)
- [實況活動低延遲設定](live-event-latency.md)
- [媒體服務實況活動錯誤碼](live-event-error-codes.md)

### <a name="tutorials-and-quickstarts"></a>教學課程和快速入門

- [教學課程：使用媒體服務即時串流](stream-live-tutorial-with-api.md)
- [使用 OBS 建立 Azure 媒體服務即時串流](live-events-obs-quickstart.md)
- [快速入門：使用入口網站上傳、編碼和串流內容](manage-assets-quickstart.md)
- [快速入門：使用 Wirecast 建立 Azure 媒體服務的即時串流](live-events-wirecast-quickstart.md)

## <a name="samples"></a>範例

您也可以 [比較程式碼範例中的 V2 和 V3 程式碼](migrate-v-2-v-3-migration-samples.md)。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]

---
title: 媒體保留單元 (Mru) 以案例為基礎的遷移指導方針 |Microsoft Docs
description: 本文提供以 MRU 案例為基礎的指導方針，可協助您從 Azure 媒體服務 V2 遷移至 V3。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 1a6bcf5d75d5331875e38e473f115f32737e42a4
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98690275"
---
# <a name="media-reserved-units-mrus-scenario-based-migration-guidance"></a>媒體保留單元 (Mru) 以案例為基礎的遷移指導方針

![遷移指南標誌](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![遷移步驟2](./media/migration-guide/steps-4.svg)

本文提供以 MRU 案例為基礎的指導方針，可協助您從 Azure 媒體服務 V2 遷移至 V3。

- 針對在 Azure 入口網站中建立的新 V3 帳戶，或使用2020-05-01 版的 V3 API，您不再需要設定媒體保留單元 (Mru) 。 系統現在會根據負載自動擴大和縮小。
- 如果您有在2020-05-01 版的 API 之前建立的 V3 或 V2 帳戶，您仍然可以使用媒體保留單元來控制作業的平行存取和效能。 如需詳細資訊，請參閱調整媒體處理。 您可以使用適用于媒體服務 V3 的 CLI 2.0，或使用 Azure 入口網站來管理 Mru。  
- 如果您在 Azure 入口網站中看不到管理 Mru 的選項，表示您執行的帳戶是使用 2020-05-01 API 或更新版本所建立的帳戶。
- 如果您熟悉將 MRU 型別設定為 S3，效能將會改善或保持不變。
- 如果您是現有的 V2 客戶，您需要建立新的 V2 帳戶，以在遷移完成之前支援現有的應用程式。 
- 尚未完全淘汰的索引子 V1 或其他媒體處理器可能需要再次啟用。 

如需 Mru 的詳細資訊，請參閱 [媒體保留單元](concept-media-reserved-units.md) 及 [如何調整媒體保留單元](media-reserved-units-cli-how-to.md)。

## <a name="mru-concepts-tutorials-and-how-to-guides"></a>MRU 概念、教學課程和操作指南

### <a name="concepts"></a>概念

[媒體保留單元](concept-media-reserved-units.md)

### <a name="how-to-guides"></a>使用說明指南

[如何調整媒體保留單元](media-reserved-units-cli-how-to.md)

## <a name="samples"></a>範例

您也可以 [比較程式碼範例中的 V2 和 V3 程式碼](migrate-v-2-v-3-migration-samples.md)。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]

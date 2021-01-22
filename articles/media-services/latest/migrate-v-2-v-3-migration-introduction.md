---
title: 從 Azure 媒體服務 v2 遷移至 v3 簡介 |Microsoft Docs
description: 本文是從媒體服務 v2 遷移至 v3 的簡介。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: fb9abd8f3186405edc31b4af48ee98482e080c68
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98690296"
---
# <a name="migrate-from-media-services-v2-to-v3-introduction"></a>從媒體服務 v2 遷移至 v3 簡介

![遷移指南標誌](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

媒體服務遷移指南可協助您從媒體服務 V2 Api 遷移至 V3 Api，並以採用現在可用的新功能和功能的遷移為基礎。 您應該使用最佳的判斷，並判斷最適合您的案例。

## <a name="how-to-use-this-guide"></a>如何使用本指南

### <a name="navigating"></a>瀏覽

在本指南中，您將會看到下圖。

![遷移步驟](./media/migration-guide/steps.svg)<br/>

您所開啟的步驟會以步驟數目的色彩變更指出，如下所示：

![遷移步驟2](./media/migration-guide/steps-2.svg)<br/>

在每個頁面的結尾，您會看到其他您可以在 [ **後續步驟]** 標題底下閱讀的其他遷移檔連結。

### <a name="guidance"></a>指引

此處提供的指引是 *一般* 的。 它包含的內容可讓您瞭解 V3 中現在提供的內容，以及媒體服務工作流程中的變更。

如需更詳細的指引，包括螢幕擷取畫面和概念圖形，請參考每個以案例為基礎的主題中的概念、教學課程、快速入門、範例和 API 參考的連結。 我們也列出可協助您比較 V2 API 與 V3 API 的範例。

## <a name="migration-guidance-overview"></a>遷移指引總覽

在您的遷移期間，有四個要遵循的一般步驟：

## <a name="step-1-benefits"></a>步驟1的優點

<hr color="#5ea0ef" size="10">

瞭解遷移至媒體服務 API V3[的優點](migrate-v-2-v-3-migration-benefits.md)。

## <a name="step-2-differences"></a>步驟2差異

<hr color="#5ea0ef" size="10">

瞭解媒體服務 V2 API 與 V3 API 之間的差異。

- [API 存取](migrate-v-2-v-3-differences-api-access.md)
- [功能間距](migrate-v-2-v-3-differences-feature-gaps.md)
- [術語和實體變更](migrate-v-2-v-3-differences-terminology.md)

## <a name="step-3-sdk-setup"></a>步驟 3 SDK 安裝程式

<hr color="#5ea0ef" size="10">

瞭解 SDK 差異，並 [設定以遷移至 V3 REST API 或用戶端 SDK](migrate-v-2-v-3-migration-setup.md)。

## <a name="step-4-scenario-based-guidance"></a>步驟4以案例為基礎的指引

<hr color="#5ea0ef" size="10">

媒體服務 V2 的應用程式可能是唯一的。 因此，我們提供了以案例為基礎的指導方針，是根據您過去 *可能* 使用媒體服務的方式，以及服務每項功能的步驟，例如：

- [編碼方式](migrate-v-2-v-3-migration-scenario-based-encoding.md)
- [即時串流](migrate-v-2-v-3-migration-scenario-based-live-streaming.md)
- [封裝和交付](migrate-v-2-v-3-migration-scenario-based-publishing.md)
- [內容保護](migrate-v-2-v-3-migration-scenario-based-content-protection.md)
- [媒體保留單元 (MRU) ](migrate-v-2-v-3-migration-scenario-based-media-reserved-units.md)

## <a name="next-steps"></a>後續步驟

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]

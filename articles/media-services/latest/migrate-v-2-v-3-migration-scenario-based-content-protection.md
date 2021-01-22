---
title: 內容保護以案例為基礎的遷移指引 |Microsoft Docs
description: 本文提供以內容保護案例為基礎的指導方針，可協助您將 Azure 媒體服務 v2 遷移至 v3 的最小值。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: a546120a93f311be29083d5f23d4716316bf64f4
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98690283"
---
# <a name="content-protection-scenario-based-migration-guidance"></a>內容保護以案例為基礎的遷移指導方針

![遷移指南標誌](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![遷移步驟2](./media/migration-guide/steps-4.svg)

本文提供以內容保護案例為基礎的指導方針，可協助您將 Azure 媒體服務 v2 遷移至 v3 的最小值。

## <a name="protect-content-in-v3-api"></a>在 v3 API 中保護內容

在新的 v3 API 中使用 [多個金鑰](design-multi-drm-system-with-access-control.md) 功能的支援。

如需特定步驟，請參閱內容保護概念、教學課程和操作說明指南。

## <a name="content-protection-concepts-tutorials-and-how-to-guides"></a>內容保護概念、教學課程和操作指南

### <a name="concepts"></a>概念

- [使用媒體服務動態加密來保護您的內容](content-protection-overview.md)
- [設計具有存取控制的多重 DRM 內容保護系統](design-multi-drm-system-with-access-control.md)
- [媒體服務 v3 與 PlayReady 授權範本](playready-license-template-overview.md)
- [具有 Widevine 授權範本的媒體服務 v3 總覽](widevine-license-template-overview.md)
- [Apple FairPlay 授權需求和設定](fairplay-license-overview.md)
- [串流原則](streaming-policy-concept.md)
- [內容金鑰原則](content-key-policy-concept.md)

### <a name="tutorials"></a>教學課程

[快速入門：使用入口網站加密內容](encrypt-content-quickstart.md)

### <a name="how-to-guides"></a>使用說明指南

- [從現有原則取得簽署金鑰](get-content-key-policy-dotnet-howto.md)
- [使用媒體服務 v3 的適用于 iOS 的離線 FairPlay 串流](offline-fairplay-for-ios.md)
- [使用媒體服務 v3 進行 Android 的離線 Widevine 串流](offline-widevine-for-android.md)
- [使用媒體服務 v3 進行 Windows 10 的離線 PlayReady 串流](offline-plaready-streaming-for-windows-10.md)

## <a name="samples"></a>範例

您也可以 [比較程式碼範例中的 V2 和 V3 程式碼](migrate-v-2-v-3-migration-samples.md)。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
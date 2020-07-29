---
title: 使用 Azure 媒體服務來建立多位元速率串流時，設定內部部署編碼器 | Microsoft Docs
description: 本主題列出的內部部署即時編碼器可用來擷取即時事件，並傳送單一位元速率即時串流到 AMS 通道 (啟用即時編碼) 以供進一步處理。 本主題列出示範如何設定列出之編碼器的教學課程連結。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 0ec6f046-0841-4673-9057-883bdbc30d5c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 5a17f98d69cb2196f92e19b66fc23dcd89bce188
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81687136"
---
# <a name="how-to-configure-on-premises-encoders-when-using-azure-media-services-to-create-multi-bitrate-streams"></a>使用 Azure 媒體服務時如何設定內部部署編碼器來建立多位元速率串流
本主題列出的內部部署即時編碼器可用來擷取即時事件，並傳送單一位元速率即時串流到 AMS 通道 (啟用即時編碼) 以供進一步處理。 本主題也列出示範如何設定列出之編碼器的教學課程連結。

> [!NOTE]
> 透過 RTMP 串流處理時，請檢查防火牆和/或 Proxy 設定，確認輸出 TCP 連接埠 1935 和 1936 已開啟。

## <a name="haivision-kb-encoder"></a>Haivision KB 編碼器
如需有關如何設定 [Haivision KB Encoder](https://www.haivision.com/products/kb-series/) 編碼器以將單一位元速率即時串流傳送到 AMS 通道的詳細資訊，請參閱[設定 Haivision KB 編碼器](media-services-configure-kb-live-encoder.md)。

## <a name="telestream-wirecast"></a>Telestream Wirecast
如需有關如何設定 [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) 編碼器，藉此傳送單一位元速率的即時串流到 AMS 通道，請參閱 [設定 Wirecast](media-services-configure-wirecast-live-encoder.md)。

## <a name="elemental-live"></a>Elemental Live
如需詳細資訊，請參閱 [Elemental Live](https://www.elemental.com/products/aws-elemental-appliances-software/#elemental-live)。

## <a name="media-services-learning-paths"></a>媒體服務學習路徑
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>後續步驟

[使用 Azure 媒體服務執行即時串流，以建立多位元速率串流](media-services-manage-live-encoder-enabled-channels.md)。


---
title: 透過 Azure 入口網站調整串流端點 | Microsoft Docs
description: 本教學課程將逐步引導您完成使用 Azure 入口網站調整串流端點的步驟。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 1008b3a3-2fa1-4146-85bd-2cf43cd1e00e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 40820170bae275f090c5f898387698fc562e59ae
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80985536"
---
# <a name="scale-streaming-endpoints-with-the-azure-portal"></a>透過 Azure 入口網站調整串流端點
## <a name="overview"></a>總覽

> [!NOTE]
> 若要完成此教學課程，您需要 Azure 帳戶。 如需詳細資訊，請參閱[Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。 
> 
> 

**進階**串流端點適合進階工作負載，提供專用並能靈活調整的頻寬容量。 擁有**進階**串流端點的客戶預設會取得一個串流單位 (SU)。 藉由新增 SU 可以調整串流端點。 每個 SU 都可為應用程式提供額外的頻寬容量。 如需有關串流端點類型和 CDN 組態的詳細資訊，請參閱[串流端點概觀](media-services-streaming-endpoints-overview.md)主題。
 
本主題說明如何調整串流端點。

如需定價詳細資料的相關資訊，請參閱[媒體服務定價詳細資料](https://azure.microsoft.com/pricing/details/media-services/)。

## <a name="scale-streaming-endpoints"></a>調整串流端點

若要變更串流端點的數目，請執行下列操作：

1. 在 [Azure 入口網站](https://portal.azure.com/)中，選取您的 Azure 媒體服務帳戶。
2. 在 [設定]**** 視窗中，選取 [串流端點]****。
3. 按一下您要調整的串流端點。 

    > [!NOTE] 
    > 您只能調整 [Premium] \(進階\)**** 串流端點。

4. 移動滑桿以指定資料流處理單位的數目。

    ![串流端點](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

## <a name="next-steps"></a>後續步驟
檢閱媒體服務學習路徑。

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]


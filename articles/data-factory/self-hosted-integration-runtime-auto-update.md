---
title: 自我裝載整合執行時間自動更新和過期通知
description: 瞭解自我裝載整合執行時間自動更新和過期通知
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
manager: shwang
ms.custom: seo-lt-2019
ms.date: 12/25/2020
ms.openlocfilehash: 6a6c897d92d469fd6247dd51f2bacb91032ac123
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98122186"
---
# <a name="self-hosted-integration-runtime-auto-update-and-expire-notification"></a>自我裝載整合執行時間自動更新和過期通知

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文將說明如何讓自我裝載整合執行時間自動更新為最新版本，以及 ADF 如何管理自我裝載整合執行時間的版本。

## <a name="self-hosted-integration-runtime-auto-update"></a>自我裝載 Integration Runtime 自動更新
一般來說，當您在本機電腦或 Azure VM 中安裝自我裝載整合執行時間時，您有兩個選項可管理自我裝載整合執行時間的版本：自動更新或手動維護。 ADF 通常每個月都會發行兩個新版的自我裝載整合執行時間，其中包括新的功能發行、bug 修正或增強功能。 因此我們建議使用者更新為最新版本，以取得最新的功能和增強功能。

最方便的方式是在您建立或編輯自我裝載整合執行時間時，啟用自動更新。 然後，它會自動更新為最新版本。 您也可以視需要在最適合的時間位置排程更新。

![啟用自動更新](media/create-self-hosted-integration-runtime/shir-auto-update.png)

您可以在自我裝載整合執行時間用戶端中檢查上次更新的日期時間。

![啟用自動更新](media/create-self-hosted-integration-runtime/shir-auto-update-2.png)

> [!NOTE]
> 為了確保自我裝載整合執行時間的穩定性，雖然我們發行兩個版本，但每個月只會自動更新一次。 因此，有時候您會發現自動更新版本是最新版本的實際版本。 如果您想要取得最新版本，您可以前往 [下載中心](https://www.microsoft.com/download/details.aspx?id=39717)。

## <a name="self-hosted-integration-runtime-expire-notification"></a>自我裝載的 Integration Runtime 過期通知
如果您想要手動控制哪個版本的自我裝載整合執行時間，您可以停用自動更新的設定，並手動安裝。 每個版本的自我裝載整合執行時間將會在一年內到期。 過期的訊息會顯示在 ADF 入口網站，以及在到期前的自我裝載整合執行時間用戶端 **90 天** 。

## <a name="next-steps"></a>後續步驟

- 檢閱 [Azure Data Factory 中的整合執行階段概念](./concepts-integration-runtime.md)。

- 了解如何[在 Azure 入口網站中建立自我裝載的整合執行階段](./create-self-hosted-integration-runtime.md)。
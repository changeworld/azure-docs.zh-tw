---
title: 應用程式和服務可用性問題常見問題
titleSuffix: Azure Cloud Services
description: 本文列出 Microsoft Azure 雲端服務之應用程式和服務可用性的相關常見問題集。
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: c294d4583ba2690e1f4952441ffb43bff1459059
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75386913"
---
# <a name="application-and-service-availability-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Azure 雲端服務之應用程式和服務可用性問題：常見問題集 (FAQ)

本文包含 [Microsoft Azure 雲端服務](https://azure.microsoft.com/services/cloud-services)之應用程式和服務可用性問題的相關常見問題集。 您也可以參閱 [雲端服務 VM 大小頁面](cloud-services-sizes-specs.md) 以取得大小資訊。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="my-role-got-recycled-was-there-any-update-rolled-out-for-my-cloud-service"></a>我的角色被回收了。 我的雲端服務是否推出任何更新？
Microsoft 大約一個月會發行一次適用於 Windows Azure PaaS VM 的新客體 OS 版本。客體作業系統只是這過程中的一項更新。 發行可能會受到許多其他因素所影響。 此外，Azure 是在成千上萬個電腦上執行。 因此，不可能預測您的角色重新啟動的確切日期和時間。 我們會在客體 OS 更新 RSS 摘要中隨時提供最新資訊，但您應將該報告的時間視為大約的值。 我們知道這對客戶造成困擾，所以正計劃對重新啟動設限或精確訂定時間。

如需最新客體 OS 更新的完整詳細資訊，請參閱 [Azure 客體 OS 版本與 SDK 相容性矩陣](cloud-services-guestos-update-matrix.md)。

如需有關重新啟動的實用資訊，以及客體和主機 OS 更新的技術細節線索，請參閱[角色執行個體因 OS 升級而重新啟動 (英文)](https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx) 的 MSDN 部落格文章。

## <a name="why-does-the-first-request-to-my-cloud-service-after-the-service-has-been-idle-for-some-time-take-longer-than-usual"></a>為什麼在服務已閒置一段時間後，對我雲端服務的第一個要求所花費時間比平常更久？
當網頁伺服器收到第一個要求時，會先重新編譯程式碼，然後再處理要求。 這就是第一個要求比其他要求更費時的原因。 依預設，應用程式集區在使用者閒置的情況下會加以關閉。 應用程式集區也會進行回收，預設為每隔 1,740 分鐘 (29 小時)。

可以定期將 Internet Information Services (IIS) 應用程式集區進行回收，以避免不穩定的狀態，可能會導致應用程式當機、停止回應，或記憶體流失等問題。

下列文件將協助您了解並減輕這個問題：
* [修正 IIS 的緩慢初始載入](https://stackoverflow.com/questions/13386471/fixing-slow-initial-load-for-iis)
* [應用程式集區回收非常緩慢之後 IIS 7.5 Web 應用程式的第一個要求](https://stackoverflow.com/questions/13917205/iis-7-5-web-application-first-request-after-app-pool-recycle-very-slow)

如果您想要變更 IIS 的預設行為，將需要使用啟動工作，因為如果您以手動方式將變更套用至 Web 角色執行個體，所做的變更最終將會遺失。

如需詳細資訊，請參閱[如何設定和執行雲端服務的啟動工作](cloud-services-startup-tasks.md)。

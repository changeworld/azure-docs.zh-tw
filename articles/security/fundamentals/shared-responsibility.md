---
title: 雲中的共用責任 - 微軟 Azure
description: 瞭解共用責任模型以及哪些安全任務由雲供應商處理，以及哪些任務由您處理。
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: na
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/16/2019
ms.author: terrylan
ms.openlocfilehash: 8f16105d6bda1798828bc423ec8a158d49e0cf2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72518365"
---
# <a name="shared-responsibility-in-the-cloud"></a>雲端中共同承擔的責任

在考慮和評估公共雲服務時，瞭解共用責任模型以及哪些安全任務由雲供應商處理以及哪些任務由您處理至關重要。 工作負載職責因工作負載是託管在軟體即服務 （SaaS）、平臺即服務 （PaaS）、基礎架構即服務 （IaaS） 還是本地資料中心而異

## <a name="division-of-responsibility"></a>責任劃分
在本地資料中心中，您擁有整個堆疊。 當您遷移到雲時，一些職責會轉移到 Microsoft。 下圖根據堆疊的部署類型說明了您和 Microsoft 之間的責任領域。

![責任區](./media/shared-responsibility/shared-responsibility.png)

就所有雲端部署類型而言，您擁有您的資料和身分識別。 您需負責保護您資料和身分識別、內部部署資源及您所控制之雲端元件 (因服務類型而異) 的安全性。

無論部署類型如何，您始終保留以下職責：

- 資料
- 端點
- 帳戶
- 存取管理

## <a name="cloud-security-advantages"></a>雲端安全性優點
雲為解決長期資訊安全挑戰提供了顯著優勢。 在內部部署環境中，組織可能責任重大但可投資在安全性上的資源卻相當有限，導致創造出一種攻擊者能夠利用所有層級弱點的環境。

下圖顯示了一種傳統方法，即由於資源有限，許多安全責任未履行。 在支援雲的方法中，您可以每天將安全職責轉移給雲供應商並重新分配資源。

![雲端時代的安全性優點](./media/shared-responsibility/cloud-enabled-security.png)

在支援雲的方法中，您還可以利用基於雲的安全功能提高有效性，並使用雲智慧來提高威脅檢測和回應時間。 藉由將責任轉移給雲端提供者，組織便可擴大安全性涵蓋範圍，而能夠將安全性資源和預算重新配置給其他業務優先順序項目。

## <a name="next-steps"></a>後續步驟
有關在 SaaS、PaaS 和 IaaS 部署中您和 Microsoft 之間的責任劃分的詳細資訊，請參閱[雲計算的共用責任](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)。

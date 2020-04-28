---
title: 雲端中的共同責任-Microsoft Azure
description: 瞭解共用責任模型，以及由雲端提供者處理哪些安全性工作，以及由您處理哪些工作。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "72518365"
---
# <a name="shared-responsibility-in-the-cloud"></a>雲端中共同承擔的責任

當您考慮並評估公用雲端服務時，請務必瞭解共用責任模型，以及由雲端提供者處理哪些安全性工作，以及由您處理哪些工作。 工作負載責任會根據工作負載是裝載于軟體即服務（SaaS）、平臺即服務（PaaS）、基礎結構即服務（IaaS）或內部部署資料中心而有所不同

## <a name="division-of-responsibility"></a>責任劃分
在內部部署資料中心內，您擁有整個堆疊。 當您移至雲端時，某些責任會轉移給 Microsoft。 下圖根據您的堆疊部署類型，說明您與 Microsoft 之間的責任區域。

![責任區](./media/shared-responsibility/shared-responsibility.png)

就所有雲端部署類型而言，您擁有您的資料和身分識別。 您需負責保護您資料和身分識別、內部部署資源及您所控制之雲端元件 (因服務類型而異) 的安全性。

不論部署的類型為何，下列責任一律由您保留：

- 資料
- 端點
- 帳戶
- 存取管理

## <a name="cloud-security-advantages"></a>雲端安全性優點
雲端提供解決長期資訊安全性挑戰的重要優點。 在內部部署環境中，組織可能責任重大但可投資在安全性上的資源卻相當有限，導致創造出一種攻擊者能夠利用所有層級弱點的環境。

下圖顯示因資源有限而不符合相依許多安全性責任的傳統方法。 在具備雲端功能的方法中，您可以將每日安全性責任轉移給您的雲端提供者，並重新配置您的資源。

![雲端時代的安全性優點](./media/shared-responsibility/cloud-enabled-security.png)

在具備雲端功能的方法中，您也可以利用雲端式安全性功能來提升效能，並使用雲端智慧來改善您的威脅偵測和回應時間。 藉由將責任轉移給雲端提供者，組織便可擴大安全性涵蓋範圍，而能夠將安全性資源和預算重新配置給其他業務優先順序項目。

## <a name="next-steps"></a>後續步驟
如需有關在 SaaS、PaaS 和 IaaS 部署中，您與 Microsoft 之間責任劃分的詳細資訊，請參閱雲端運算的[共同責任](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)。

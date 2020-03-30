---
title: 威脅情報&安全警報映射 - Azure 安全中心
description: 了解如何使用「Azure 資訊安全中心」內的安全性警訊地圖和威脅情報功能，來識別您 VM 和電腦中的潛在威脅。
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a771a3a1-2925-46ca-8e27-6f6a0746f58b
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/3/2018
ms.author: rkarlin
ms.openlocfilehash: 39835d9d73adcbe474d3b70dfced313e18d1a3b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603427"
---
# <a name="security-alerts-map-and-threat-intelligence"></a>安全性警示地圖和威脅情報
本文可協助您使用的 Azure 資訊安全中心安全性警訊地圖和以安全性事件為基礎的威脅情報地圖，以解決安全性相關問題。

> [!NOTE]
> "安全*事件*映射"按鈕已于 2019 年 7 月 31 日停用。 有關詳細資訊和替代服務，請參閱[安全中心停用功能（2019 年 7 月）。](security-center-features-retirement-july2019.md#menu_securityeventsmap)


## <a name="how-the-security-alerts-map-works"></a>安全性警訊地圖地圖的運作方式
資訊安全中心會提供地圖，協助您識別環境的安全性威脅。 例如，您可以識別特定的電腦是否屬於殭屍網路，以及威脅來自何處。 攻擊者違法安裝惡意程式碼，暗中操作命令和控制項來管理殭屍網路時，則電腦會成為殭屍網路中的節點。 

為了建置此地圖，資訊安全中心會使用來自 Microsoft 內多個來源的資料。 資訊安全中心將會使用這項資料來對應您環境的潛在威脅。 

[安全性事件回應程序](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response)的其中一個步驟是識別危害系統的嚴重性。 在這個階段中，您應該執行下列工作︰

- 判斷攻擊本質。
- 判斷攻擊起源點。
- 判斷攻擊意圖。 在組織中指示還是隨機指示攻擊取得特定資訊？
- 識別已遭入侵的系統。
- 識別已存取的檔案，並判斷這些檔案的敏感度。

您可以使用資訊安全中心的安全性警訊地圖來協助進行這些工作。

## <a name="access-the-security-alerts-map"></a>存取安全性警訊地圖
若要以視覺化方式檢視目前在環境中的威脅，請開啟安全性警訊地圖：

1. 開啟 [資訊安全中心]**** 儀表板。
2. 在左窗格中的 [威脅防護]**** 下，選取 [安全性警訊地圖]****。 地圖隨即開啟。
3. 若要取得警訊的詳細資訊，並獲得補救步驟，請按一下地圖上的警示點，並遵循指示進行操作。 
 
安全性警訊地圖是以警示為基礎。 這些警示所依據的網路通訊活動取決於成功解析的 IP 位址、IP 位址是否為已知有風險的 IP 位址 (例如，已知的 cryptominer) 或先前未辨識為有風險的 IP 位址。 地圖會提供您先前在 Azure 中選取任何訂用帳戶之間的警示。 

在地圖上的警示會根據偵測為來源的地理位置顯示，並且以色彩區分嚴重性。 
    ![威脅情報資訊](./media/security-center-threat-intel/security-center-alert-map.png)



## <a name="see-also"></a>另請參閱
在這篇文章中，您已學會如何使用資訊安全中心的威脅情報，可協助您識別可疑的活動。 如要深入了解資訊安全中心，請參閱下列文章：

* [管理和回應 Azure 安全中心中的安全警報](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)。 了解如何在資訊安全中心管理警示，以及回應安全性事件。
* [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md)。 了解如何監視 Azure 資源的健全狀況。
* [了解 Azure 資訊安全中心的安全性警示](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)。 了解不同類型的安全性警示。
* [Azure 安全中心故障排除指南](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide)。 了解如何針對資訊安全中心的常見問題進行疑難排解。
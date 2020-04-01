---
title: 在 Azure 資訊安全中心管理安全性警示 | Microsoft Docs
description: 本文件可協助您使用「Azure 資訊安全中心」功能來管理及回應安全性警示。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 8e44ce594375deeac47f037515d96c57d15c8359
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398395"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>在 Azure 資訊安全中心管理和回應安全性警示

本主題介紹如何查看和處理您收到的警報,以保護資源。 

* 要瞭解不同類型的警報,請參閱[安全警報類型](alerts-reference.md)。
* 有關安全中心如何生成警報的概述,請參閱[Azure 安全中心如何檢測和回應威脅](security-center-alerts-overview.md)。

> [!NOTE]
> 若要啟用進階偵測，請升級至 Azure 資訊安全中心標準。 有免費試用版可用。 要升級,請在[安全策略](tutorial-security-policy.md)中選擇定價層。 若要深入了解，請參閱 [Azure 資訊安全中心價格](security-center-pricing.md)。

## <a name="what-are-security-alerts"></a>什麼是安全性警示：
資訊安全中心會自動收集、分析及整合您 Azure 資源、網路和已連線的合作夥伴解決方案 (例如防火牆和端點保護解決方案) 的記錄檔資料，來偵測真正的威脅並減少誤判情形。 「資訊安全中心」會顯示優先安全性警示清單，以及需要您快速調查問題的資訊，和如何修復攻擊行為的建議。

> [!NOTE]
> 有關安全中心檢測功能工作方式的詳細資訊,請參閱[Azure 安全中心如何檢測和回應威脅](security-center-alerts-overview.md#detect-threats)。

## <a name="manage-your-security-alerts"></a>管理安全警報

1. 在"安全中心"儀錶板中,請參閱 **"威脅保護**"磁貼以查看警報並概述警報。

    ![資訊安全中心的 [安全性警示] 圖格](./media/security-center-managing-and-responding-alerts/security-center-dashboard-alert.png)

1. 要查看有關警報的更多詳細資訊,請單擊磁貼。

   ![資訊安全中心內的安全性警示](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. 要篩選顯示的警報,請按下 **「篩選 」,** 然後從打開的 **「篩選器」** 邊欄選項卡中選擇要應用的篩選器選項。 清單根據選取篩選器更新。 篩選非常有用。 例如，您可能想確認在過去 24 小時發生的安全性警示，因為您正在調查系統中可能的入侵行動。

    ![篩選資訊安全中心的警示](./media/security-center-managing-and-responding-alerts/security-center-filter-alerts.png)

## <a name="respond-to-security-alerts"></a>回應安全性警示

1. 從 **「安全警報」** 清單中,按一下安全警報。 將顯示所涉及的資源和修復攻擊所需的步驟。

    ![回應安全性警示](./media/security-center-managing-and-responding-alerts/security-center-alert.png)

1. 查看資訊后,單擊受到攻擊的資源。

    ![有關如何處理安全警報的建議](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    "**一般資訊**"部分可以深入瞭解觸發安全警報的原因。 它顯示目標資源、源 IP 位址(如果適用)、警報仍處於活動狀態以及有關如何修復的建議等資訊。  

    > [!NOTE]
    >在某些情況下,源 IP 位址不可用,某些 Windows 安全事件日誌不包含 IP 位址。

1. 資訊安全中心會根據安全性警訊，建議您不同的補救步驟。 每個警報都跟隨它們。 

    在某些情況下,為了緩解安全警報,您可能需要使用其他 Azure 控件或服務來實現建議的修正。 

## <a name="see-also"></a>另請參閱

在本文件中，您了解到如何在資訊安全中心設定安全性原則。 如要深入了解資訊安全中心，請參閱下列主題：

- [Microsoft 學習模組,介紹如何使用 Azure 安全中心的警報功能來監視和回應威脅](https://docs.microsoft.com/learn/modules/resolve-threats-with-azure-security-center/)
* [Azure 安全中心中的安全警報](security-center-alerts-overview.md)。
* [處理安全事件](security-center-incident.md)
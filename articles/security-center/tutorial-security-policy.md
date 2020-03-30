---
title: 使用安全性原則 | Microsoft Docs
description: 本文介紹如何使用 Azure 安全中心中的安全性原則。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 45a27be400753b56c42310a340334feba8a420c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73906875"
---
# <a name="working-with-security-policies"></a>使用安全性原則

這篇文章說明如何設定安全性原則，以及如何在資訊安全中心中檢視它們。 

## <a name="introduction-to-security-policies"></a>安全性原則簡介

安全性原則定義了工作負載所需的配置，並有助於確保您遵守公司或監管機構的安全要求。

Azure 安全中心會根據您選擇的策略制定其安全建議。 安全中心策略基於 Azure 策略中創建的策略計畫。 可以使用[Azure 策略](../governance/policy/overview.md)管理原則，並跨管理組和多個訂閱設置策略。

安全中心提供以下選項，用於使用安全性原則：

* **查看和編輯內置預設策略**- 啟用安全中心時，名為"ASC 預設"的內置計畫將自動分配給所有安全中心註冊訂閱（免費或標準層）。 要自訂此計畫，可以啟用或禁用其中的各個策略。 請參閱[內置安全性原則](security-center-policy-definitions.md)清單，瞭解現成的可用選項。

* **添加您自己的自訂策略**- 如果要自訂應用於訂閱的安全計畫，可以在安全中心內執行此操作。 然後，如果電腦未遵循您創建的策略，您將收到建議。 有關構建和分配自訂策略的說明，請參閱[使用自訂安全性原則](custom-security-policies.md)。

* **添加法規遵從性策略**- 安全中心的法規合規性儀表板顯示環境中特定標準或法規（如 Azure CIS、NIST SP 800-53 R4、SWIFT CSP CSCF-v2020）環境中所有評估的狀態。 有關詳細資訊，請參閱[提高合規性](security-center-compliance-dashboard.md)。


## <a name="managing-your-security-policies"></a>管理安全性原則

若要在資訊安全中心檢視安全性原則：

1. 在 [資訊安全中心]**** 儀表板上，選取 [安全性解決方案]****。

    ![原則管理窗格](./media/security-center-policies/security-center-policy-mgt.png)

   在 [原則管理]**** 畫面中，您可以看到管理群組、訂用帳戶和工作區數目，以及您的管理群組結構。

1. 選取要檢視其原則的訂用帳戶或管理群組。

1. 將顯示該訂閱或管理組的安全性原則頁。 它顯示可用和分配的策略。

   ![[原則] 畫面](./media/tutorial-security-policy/security-policy-page.png)

    > [!NOTE]
    > 如果預設策略旁邊有一個標籤"MG 繼承"，則意味著策略已分配給管理組並由您查看的訂閱繼承。


1. 從此頁面上的可用選項中進行選擇：

    1. 要使用產業政策，請按一下"**添加更多標準**"。 有關詳細資訊，請參閱[更新到動態合規性包](update-regulatory-compliance-packages.md)。

    1. 要分配和管理自訂計畫，請按一下"**添加自訂計畫**"。 有關詳細資訊，請參閱[使用自訂安全性原則](custom-security-policies.md)。

    1. 要查看和編輯預設策略，請按一下"**查看有效原則**"，然後按如下說明繼續操作。 

       ![[原則] 畫面](./media/security-center-policies/policy-screen.png)
       
       此**安全性原則**螢幕反映所選訂閱或管理組上分配的策略執行的操作。
       
       * 使用頂部的連結打開適用于訂閱或管理組的策略**分配**。 這些連結允許您訪問分配並編輯或禁用策略。 例如，如果您看到特定策略分配有效地拒絕終結點保護，請使用連結編輯或禁用策略。
       
       * 在原則清單中，您可以在訂用帳戶或管理群組中查看原則的有效應用。 將考慮適用于範圍的每個策略的設置，並顯示策略所執行的操作的累積結果。 例如，如果在策略的一個分配中禁用，但在另一個分配中，它設置為"審核存在"，則累積效果將應用 AuditIfNot 存在。 更積極的效果一律會優先使用。
       
       * 原則的效果可以是：Append、Audit、AuditIfNotExists、Deny、DeployIfNotExists、Disabled。 如需如何套用效果的相關詳細資訊，請參閱[了解原則效果](../governance/policy/concepts/effects.md)。

       > [!NOTE]
       > 檢視指派的原則時，您可以看到多個指派，您可以看到每個指派如何自行設定。


## <a name="who-can-edit-security-policies"></a>誰可以編輯安全性原則？

您可以透過 Azure 原則入口網站、透過 REST API 或使用 Windows PowerShell 編輯安全性原則。

資訊安全中心會使用角色型存取控制 (RBAC)，以提供可在 Azure 中指派給使用者、群組與服務的內建角色。 使用者開啟資訊安全中心時，只能看到與自己能夠存取之資源相關的資訊。 這意味著使用者被分配給資源訂閱*的擁有者*、*參與者*或*讀取器*的角色。 除了這些角色外，還有兩個特定的安全中心角色：

- **安全讀取器**：具有安全中心的存取權限，其中包括建議、警報、策略和運行狀況，但不能進行更改。
- **安全管理員**：具有與*安全讀取器*相同的視圖許可權，還可以更新安全性原則並取消建議和警報。


## <a name="disable-security-policies"></a>禁用安全性原則
如果預設安全性原則正在生成與您的環境無關的建議，則可以通過禁用發送建議的策略定義來阻止它。
有關建議的詳細資訊，請參閱[管理安全建議](security-center-recommendations.md)。

1. 在安全中心中，從**策略&合規性**部分中，按一下 **"安全性原則**"。

   ![政策管理](./media/tutorial-security-policy/policy-management.png)

2. 按一下要為其禁用建議的訂閱或管理組。

   > [!NOTE]
   > 請記住，管理群組會將其原則套用至其訂用帳戶。 因此，如果您停用訂用帳戶的原則，而且訂用帳戶屬於仍使用相同原則的管理群組，則您會繼續收到原則建議。 原則仍然會從管理層級套用，而且仍會產生建議。

1. 按一下 **"查看有效的策略**"。

   ![禁用策略](./media/tutorial-security-policy/view-effective-policy.png)

1. 按一下分配的策略。

   ![禁用策略](./media/tutorial-security-policy/security-policy.png)

1. 在 **"參數"** 部分中，搜索調用要禁用的建議的策略，並從下拉清單中選擇 **"禁用"**

   ![禁用策略](./media/tutorial-security-policy/disable-policy.png)

1. 按一下 [儲存]****。

   > [!NOTE]
   > 禁用策略更改最多可能需要 12 小時才能生效。



## <a name="next-steps"></a>後續步驟
在本文中，您瞭解了安全性原則。 有關相關資訊，請參閱以下文章：

* 有關如何使用 PowerShell 設置策略的說明，請參閱[快速入門：創建策略分配，使用 Azure PowerShell 模組標識不合規資源](../governance/policy/assign-policy-powershell.md)

* 有關如何編輯 Azure 策略中的安全性原則的說明，請參閱[創建和管理原則以強制實施合規性](../governance/policy/tutorials/create-and-manage.md)。

* 有關如何使用 Azure 策略跨訂閱或管理組設置策略的說明，請參閱什麼是 Azure[策略？](../governance/policy/overview.md)
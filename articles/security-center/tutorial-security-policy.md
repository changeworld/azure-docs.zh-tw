---
title: 使用安全性原則 | Microsoft Docs
description: 本文說明如何在 Azure 資訊安全中心中使用安全性原則。
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
ms.openlocfilehash: c98ae7c95ac3fc186786612dd3d8d8bd55fa816f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82024875"
---
# <a name="working-with-security-policies"></a>使用安全性原則

這篇文章說明如何設定安全性原則，以及如何在資訊安全中心中檢視它們。 

## <a name="introduction-to-security-policies"></a>安全性原則簡介

安全性原則會定義您的工作負載所需的設定，並協助確保您符合公司或法規的安全性需求。

Azure 資訊安全中心根據您選擇的原則來做出其安全性建議。 資訊安全中心原則是以 Azure 原則中建立的原則計畫為基礎。 您可以使用[Azure 原則](../governance/policy/overview.md)來管理您的原則，以及跨管理群組和跨多個訂用帳戶設定原則。

資訊安全中心提供使用安全性原則的下列選項：

* **查看和編輯內建的預設原則**-當您啟用資訊安全中心時，名為 ' ASC default ' 的內建計畫會自動指派給所有資訊安全中心已註冊的訂用帳戶（免費或標準層）。 若要自訂此計畫，您可以在其中啟用或停用個別原則。 請參閱[內建安全性原則](security-center-policy-definitions.md)清單，以瞭解現成可用的選項。

* **新增您自己的自訂原則**-如果您想要自訂套用至訂用帳戶的安全性計畫，您可以在資訊安全中心內這麼做。 如果您的電腦未遵循您所建立的原則，您就會收到建議。 如需建立和指派自訂原則的指示，請參閱[使用自訂安全性原則](custom-security-policies.md)。

* **新增法規合規性原則**-資訊安全中心的 [法規合規性] 儀表板會顯示您環境中特定標準或法規（例如 Azure CI、NIST SP 800-53 R4、SWIFT CSP CSCF-v2020）內容中所有評量的狀態。 如需詳細資訊，請參閱[改善您的法規合規性](security-center-compliance-dashboard.md)。


## <a name="managing-your-security-policies"></a>管理您的安全性原則

若要在資訊安全中心檢視安全性原則：

1. 在 [資訊安全中心]**** 儀表板上，選取 [安全性解決方案]****。

    ![原則管理窗格](./media/security-center-policies/security-center-policy-mgt.png)

   在 [原則管理]**** 畫面中，您可以看到管理群組、訂用帳戶和工作區數目，以及您的管理群組結構。

1. 選取要檢視其原則的訂用帳戶或管理群組。

1. 該訂用帳戶或管理群組的 [安全性原則] 頁面隨即出現。 它會顯示可用和已指派的原則。

   ![[原則] 畫面](./media/tutorial-security-policy/security-policy-page.png)

    > [!NOTE]
    > 如果您的預設原則有「MG 繼承」標籤，表示該原則已指派給管理群組，並由您正在查看的訂用帳戶繼承。


1. 選擇此頁面上可用的選項：

    1. 若要使用產業原則，請選取 [**新增更多標準**]。 如需詳細資訊，請參閱[更新為動態合規性套件](update-regulatory-compliance-packages.md)。

    1. 若要指派和管理自訂方案，請選取 [**新增自訂方案**]。 如需詳細資訊，請參閱[使用自訂安全性原則](custom-security-policies.md)。

    1. 若要查看和編輯預設原則，請選取 [**查看有效原則**]，然後依照下面所述繼續進行。 

       ![[原則] 畫面](./media/security-center-policies/policy-screen.png)
       
       此**安全性原則**畫面會反映指派給您所選取之訂用帳戶或管理群組的原則所採取的動作。
       
       * 使用頂端的連結來開啟適用于訂用帳戶或管理群組的原則**指派**。 這些連結可讓您存取指派，以及編輯或停用原則。 例如，如果您看到特定原則指派有效地拒絕 endpoint protection，請使用連結來編輯或停用原則。
       
       * 在原則清單中，您可以在訂用帳戶或管理群組中查看原則的有效應用。 系統會將套用至範圍的每個原則設定納入考慮，並顯示原則所採取之動作的累計結果。 例如，如果原則的某個指派已停用，但另一個已設定為 AuditIfNotExist，則累計效果會套用 AuditIfNotExist。 更積極的效果一律會優先使用。
       
       * 原則的效果可以是：Append、Audit、AuditIfNotExists、Deny、DeployIfNotExists、Disabled。 如需如何套用效果的相關詳細資訊，請參閱[了解原則效果](../governance/policy/concepts/effects.md)。

       > [!NOTE]
       > 檢視指派的原則時，您可以看到多個指派，您可以看到每個指派如何自行設定。


## <a name="who-can-edit-security-policies"></a>誰可以編輯安全性原則？

您可以透過 Azure 原則入口網站、透過 REST API 或使用 Windows PowerShell 編輯安全性原則。

資訊安全中心會使用角色型存取控制 (RBAC)，以提供可在 Azure 中指派給使用者、群組與服務的內建角色。 使用者開啟資訊安全中心時，只能看到與自己能夠存取之資源相關的資訊。 這表示系統會將*擁有*者、*參與者*或*讀取*者角色指派給資源的訂用帳戶。 除了這些角色，還有兩個特定的資訊安全中心角色：

- **安全性讀取者**：具有資訊安全中心的 view 許可權，其中包括建議、警示、原則和健康情況，但無法進行變更。
- **安全性系統管理員**：擁有與*安全性讀取*者相同的 view 許可權，而且也可以更新安全性原則，並解除建議和警示。


## <a name="disable-security-policies"></a>停用安全性原則
如果預設的安全性原則正在產生與您的環境無關的建議，您可以停用傳送建議的原則定義來停止它。
如需有關建議的詳細資訊，請參閱[管理安全性建議](security-center-recommendations.md)。

1. 在資訊安全中心中，從 [**原則 & 相容性**] 區段中，選取 [**安全性原則**]。

   ![原則管理](./media/tutorial-security-policy/policy-management.png)

2. 選取您想要停用建議的訂用帳戶或管理群組。

   > [!NOTE]
   > 請記住，管理群組會將其原則套用至其訂用帳戶。 因此，如果您停用訂用帳戶的原則，而且訂用帳戶屬於仍使用相同原則的管理群組，則您會繼續收到原則建議。 原則仍然會從管理層級套用，而且仍會產生建議。

1. 選取 [**查看有效原則**]。

   ![停用原則](./media/tutorial-security-policy/view-effective-policy.png)

1. 選取指派的原則。

   ![停用原則](./media/tutorial-security-policy/security-policy.png)

1. 在 [**參數**] 區段中，搜尋叫用您想要停用之建議的原則，然後從下拉式清單中選取 [**已停用**]

   ![停用原則](./media/tutorial-security-policy/disable-policy.png)

1. 選取 [儲存]。

   > [!NOTE]
   > 停用原則變更最多可能需要12小時才會生效。



## <a name="next-steps"></a>後續步驟
在本文中，您已瞭解安全性原則。 如需相關資訊，請參閱下列文章：

* 如需有關如何使用 PowerShell 設定原則的指示，請參閱[快速入門：使用 Azure PowerShell 模組建立原則指派，以識別不符合規範的資源](../governance/policy/assign-policy-powershell.md)

* 如需如何在 Azure 原則中編輯安全性原則的指示，請參閱[建立和管理原則以強制執行合規性](../governance/policy/tutorials/create-and-manage.md)。

* 如需有關如何使用 Azure 原則在訂用帳戶或管理群組上設定原則的指示，請參閱[什麼是 Azure 原則？](../governance/policy/overview.md)
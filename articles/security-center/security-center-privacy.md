---
title: 在 Azure 資訊安全中心管理使用者資料 | Microsoft Docs
description: 瞭解如何在 Azure 資訊安全中心中管理使用者資料。 管理使用者資料包含了存取、刪除或匯出資料的能力。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2018
ms.author: memildin
ms.openlocfilehash: bf715d872fab421de30ebcb146a1981a7d008738
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80585978"
---
# <a name="manage-user-data-in-azure-security-center"></a>在 Azure 資訊安全中心管理使用者資料
本文提供有關如何在 Azure 資訊安全中心管理使用者資料的資訊。 管理使用者資料包含了存取、刪除或匯出資料的能力。

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

指派為讀者、擁有者、參與者或帳戶管理員角色的資訊安全中心使用者，可以在此工具中存取客戶資料。 若要深入瞭解帳戶管理員角色，請參閱[Azure 角色型存取控制的內建角色](../role-based-access-control/built-in-roles.md)，以深入瞭解讀取者、擁有者和參與者角色。 請參閱[Azure 訂](../cost-management-billing/manage/add-change-subscription-administrator.md)用帳戶管理員。

## <a name="searching-for-and-identifying-personal-data"></a>搜尋及識別個人資料
資訊安全中心使用者可以透過 Azure 入口網站檢視他們的個人資料。 資訊安全中心只會儲存安全性連絡人詳細資料，例如電子郵件地址和電話號碼。 如需詳細資訊，請參閱[Azure 資訊安全中心中的提供安全性連絡人詳細資料](security-center-provide-security-contact-details.md)。

在 Azure 入口網站中，使用者可以使用資訊安全中心的即時 VM 存取功能來查看允許的 IP 設定。 如需詳細資訊，請參閱[使用即時管理虛擬機器存取](security-center-just-in-time.md)。

在 Azure 入口網站中，使用者可以檢視資訊安全中心提供的安全性警示，包括 IP 位址和攻擊者詳細資料。 如需詳細資訊，請參閱[管理和回應 Azure 資訊安全中心中的安全性警示](security-center-managing-and-responding-alerts.md)。

## <a name="classifying-personal-data"></a>分類個人資料
您不需要分類資訊安全中心的安全性連絡人功能中找到的個人資料。 儲存的資料只有電子郵件地址 (或多個電子郵件地址) 和電話號碼。 [連絡人資料](security-center-provide-security-contact-details.md)會由資訊安全中心進行驗證。

您不需要分類資訊安全中心的[即時](security-center-just-in-time.md)功能所儲存的 IP 位址和埠號碼。

只有指派為管理員角色的使用者可以藉由在資訊安全中心[檢視警示](security-center-managing-and-responding-alerts.md)，對個人資料進行分類。

## <a name="securing-and-controlling-access-to-personal-data"></a>保護與控制個人資料存取
指派為讀者、擁有者、參與者或帳戶管理員角色的資訊安全中心使用者，可以存取[安全性連絡人資料](security-center-provide-security-contact-details.md)。

指派為讀者、擁有者、參與者或帳戶管理員角色的資訊安全中心使用者，可以存取其[及時](security-center-just-in-time.md)原則。

指派為讀者、擁有者、參與者或帳戶管理員角色的資訊安全中心使用者，可以檢視其[警示](security-center-managing-and-responding-alerts.md)。

## <a name="updating-personal-data"></a>更新個人資料
指派為擁有者、參與者或帳戶管理員角色的資訊安全中心使用者，可以透過 Azure 入口網站更新[安全性連絡人資料](security-center-provide-security-contact-details.md)。

指派為擁有者、參與者或帳戶管理員角色的資訊安全中心使用者，可以更新其[及時原則](security-center-just-in-time.md)。

帳戶管理員無法編輯警示事件。 [警示事件](security-center-managing-and-responding-alerts.md)會視為安全性資料且為唯讀。

## <a name="deleting-personal-data"></a>刪除個人資料
指派為擁有者、參與者或帳戶管理員角色的資訊安全中心使用者，可以透過 Azure 入口網站刪除[安全性連絡人資料](security-center-provide-security-contact-details.md)。

指派為擁有者、參與者或帳戶管理員角色的資訊安全中心使用者，可以透過 Azure 入口網站刪除[及時原則](security-center-just-in-time.md)。

資訊安全中心使用者無法刪除警示事件。 基於安全性理由，[警示事件](security-center-managing-and-responding-alerts.md)會被視為唯讀資料。

## <a name="exporting-personal-data"></a>匯出個人資料
指派為讀者、擁有者、參與者或帳戶管理員角色的資訊安全中心使用者，可透過下列方式匯出[安全性連絡人資料](security-center-provide-security-contact-details.md)：

- 從 Azure 入口網站複製
- 執行 Azure REST API 呼叫，GET HTTP：
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/Microsoft.Security/securityContacts?api-version={api-version}
  ```

指派為帳戶管理員角色的資訊安全中心使用者，可以藉由下列方式，匯出包含 IP 位址的[即時原則](security-center-just-in-time.md)：

- 從 Azure 入口網站複製
- 執行 Azure REST API 呼叫，GET HTTP：
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Security/locations/{location}/jitNetworkAccessPolicies/default?api-version={api-version}
  ```

帳戶管理員可以透過下列方式，匯出警示詳細資料：

- 從 Azure 入口網站複製
- 執行 Azure REST API 呼叫，GET HTTP：
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/microsoft.Security/alerts?api-version={api-version}
  ```

如需詳細資訊，請參閱[取得安全性警示（取得集合）](https://msdn.microsoft.com/library/mt704050.aspx)。

## <a name="restricting-the-use-of-personal-data-for-profiling-or-marketing-without-consent"></a>限制在未經同意下使用個人資料進行分析或行銷
資訊安全中心使用者可以選擇藉由刪除其[安全性連絡人資料](security-center-provide-security-contact-details.md)來退出。

[及時的資料](security-center-just-in-time.md)會被視為不可識別的資料，並保留30天的時間。

[警示資料](security-center-managing-and-responding-alerts.md)會視為安全性資料，保留兩年。

## <a name="auditing-and-reporting"></a>稽核與報告
系統會在[Azure 活動記錄](../azure-monitor/platform/platform-logs-overview.md)中維護安全性連絡人、及時和警示更新的審核記錄。
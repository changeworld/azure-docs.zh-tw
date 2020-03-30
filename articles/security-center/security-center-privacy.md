---
title: 在 Azure 資訊安全中心管理使用者資料 | Microsoft Docs
description: 瞭解如何在 Azure 安全中心管理使用者資料。 管理使用者資料包含了存取、刪除或匯出資料的能力。
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
ms.openlocfilehash: 6edea1d0de53e2dc9f764de26209dc1f3110556e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978608"
---
# <a name="manage-user-data-in-azure-security-center"></a>在 Azure 資訊安全中心管理使用者資料
本文提供有關如何在 Azure 資訊安全中心管理使用者資料的資訊。 管理使用者資料包含了存取、刪除或匯出資料的能力。

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

指派為讀者、擁有者、參與者或帳戶管理員角色的資訊安全中心使用者，可以在此工具中存取客戶資料。 要瞭解有關帳戶管理員角色詳細資訊，請參閱[Azure 基於角色的訪問控制項的內置角色](../role-based-access-control/built-in-roles.md)，以瞭解有關"讀者"、擁有者角色和參與者角色。 請參閱[Azure 訂閱管理員](../cost-management-billing/manage/add-change-subscription-administrator.md)。

## <a name="searching-for-and-identifying-personal-data"></a>搜尋及識別個人資料
資訊安全中心使用者可以透過 Azure 入口網站檢視他們的個人資料。 資訊安全中心只會儲存安全性連絡人詳細資料，例如電子郵件地址和電話號碼。 有關詳細資訊，請參閱在[Azure 安全中心中提供安全連絡人詳細資訊](security-center-provide-security-contact-details.md)。

在 Azure 門戶中，使用者可以使用安全中心的即時 VM 訪問功能查看允許的 IP 配置。 有關詳細資訊，請參閱[使用及時管理虛擬機器訪問](security-center-just-in-time.md)。

在 Azure 入口網站中，使用者可以檢視資訊安全中心提供的安全性警示，包括 IP 位址和攻擊者詳細資料。 有關詳細資訊，請參閱在[Azure 安全中心中管理和回應安全警報](security-center-managing-and-responding-alerts.md)。

## <a name="classifying-personal-data"></a>分類個人資料
您無需對安全中心的安全連絡人功能中找到的個人資料進行分類。 儲存的資料只有電子郵件地址 (或多個電子郵件地址) 和電話號碼。 [連絡人資料](security-center-provide-security-contact-details.md)會由資訊安全中心進行驗證。

您無需對安全中心[及時](security-center-just-in-time.md)功能保存的 IP 位址和埠號進行分類。

只有指派為管理員角色的使用者可以藉由在資訊安全中心[檢視警示](security-center-managing-and-responding-alerts.md)，對個人資料進行分類。

## <a name="securing-and-controlling-access-to-personal-data"></a>保護與控制個人資料存取
指派為讀者、擁有者、參與者或帳戶管理員角色的資訊安全中心使用者，可以存取[安全性連絡人資料](security-center-provide-security-contact-details.md)。

分配了讀取器、擁有者、參與者或帳戶管理員角色的安全中心使用者可以訪問其[即時](security-center-just-in-time.md)策略。

指派為讀者、擁有者、參與者或帳戶管理員角色的資訊安全中心使用者，可以檢視其[警示](security-center-managing-and-responding-alerts.md)。

## <a name="updating-personal-data"></a>更新個人資料
指派為擁有者、參與者或帳戶管理員角色的資訊安全中心使用者，可以透過 Azure 入口網站更新[安全性連絡人資料](security-center-provide-security-contact-details.md)。

分配了擁有者、參與者或帳戶管理員角色的安全中心使用者可以更新其[即時策略](security-center-just-in-time.md)。

帳戶管理員無法編輯警報事件。 [警示事件](security-center-managing-and-responding-alerts.md)會視為安全性資料且為唯讀。

## <a name="deleting-personal-data"></a>刪除個人資料
指派為擁有者、參與者或帳戶管理員角色的資訊安全中心使用者，可以透過 Azure 入口網站刪除[安全性連絡人資料](security-center-provide-security-contact-details.md)。

分配了擁有者、參與者或帳戶管理員角色的安全中心使用者可以通過 Azure 門戶刪除[及時策略](security-center-just-in-time.md)。

安全中心使用者無法刪除警報事件。 出於安全原因，[警報事件](security-center-managing-and-responding-alerts.md)被視為唯讀資料。

## <a name="exporting-personal-data"></a>匯出個人資料
指派為讀者、擁有者、參與者或帳戶管理員角色的資訊安全中心使用者，可透過下列方式匯出[安全性連絡人資料](security-center-provide-security-contact-details.md)：

- 從 Azure 門戶複製
- 執行 Azure REST API 呼叫，GET HTTP：
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/Microsoft.Security/securityContacts?api-version={api-version}
  ```

分配了帳戶管理員角色的安全中心使用者可以通過以下策略匯出包含 IP 位址[的及時策略](security-center-just-in-time.md)：

- 從 Azure 門戶複製
- 執行 Azure REST API 呼叫，GET HTTP：
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Security/locations/{location}/jitNetworkAccessPolicies/default?api-version={api-version}
  ```

帳戶管理員可以透過下列方式，匯出警示詳細資料：

- 從 Azure 門戶複製
- 執行 Azure REST API 呼叫，GET HTTP：
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/microsoft.Security/alerts?api-version={api-version}
  ```

有關詳細資訊，請參閱[獲取安全警報（GET 集合）。](https://msdn.microsoft.com/library/mt704050.aspx)

## <a name="restricting-the-use-of-personal-data-for-profiling-or-marketing-without-consent"></a>限制在未經同意下使用個人資料進行分析或行銷
資訊安全中心使用者可以選擇藉由刪除其[安全性連絡人資料](security-center-provide-security-contact-details.md)來退出。

[即時資料](security-center-just-in-time.md)被視為不可識別資料，並保留 30 天。

[警示資料](security-center-managing-and-responding-alerts.md)會視為安全性資料，保留兩年。

## <a name="auditing-and-reporting"></a>稽核與報告
審核安全連絡人、及時和警報更新的稽核記錄在[Azure 活動日誌](../azure-monitor/platform/platform-logs-overview.md)中維護。

## <a name="next-steps"></a>後續步驟
如需有關如何管理使用者資料的詳細資訊，請參閱[管理在 Azure 資訊安全中心調查中找到的使用者資料](security-center-investigation-user-data.md)。

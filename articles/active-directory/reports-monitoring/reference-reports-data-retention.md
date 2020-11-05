---
title: Azure AD 儲存報表資料的時間長度為何？ | Microsoft Docs
description: 瞭解 Azure 儲存各種類型的報告資料的時間長度。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/05/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98b9a2da11ad32e35704a49cfcf1788f95276dda
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93393450"
---
# <a name="how-long-does-azure-ad-store-reporting-data"></a>Azure AD 儲存報表資料的時間長度為何？


在本文中，您會了解 Azure Active Directory 中不同活動報告的資料保留原則。 

### <a name="when-does-azure-ad-start-collecting-data"></a>Azure AD 何時會開始收集資料？

| Azure AD 版本 | 開始收集 |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | 當您註冊訂用帳戶 |
| Azure AD Free| 首次開啟 [Azure Active Directory 刀鋒視窗](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)或使用[報告 API](./overview-reports.md)  |

---

### <a name="when-is-the-activity-data-available-in-the-azure-portal"></a>何時可在 Azure 入口網站中取得活動資料？

- **立即** -如果您已經使用 Azure 入口網站中的報表。
- **2 小時內** - 如果您尚未在 Azure 入口網站中開啟報告功能。

---

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>在取得進階授權之後，我能夠多快能看見活動資料？

如果您在使用免費授權時即已經有活動資料，您便可以在升級時立即看見該資料。 如果您沒有任何資料，則您升級到進階授權後，必須花一或兩天的時間，資料才會顯示在報告中。

---

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>Azure AD 何時會開始收集安全性訊號資料？  

對於安全性訊號，收集程序會在您選擇使用 **Identity Protection 中心** 時啟動。 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>Azure AD 會將資料儲存多久？

**活動報告**    

| Report                 | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| 稽核記錄             | 7 天        | 30 天             | 30 天             |
| 登入               | 7 天        | 30 天             | 30 天             |
| Azure MFA 使用方式        | 30 天       | 30 天             | 30 天             |

使用 Azure 監視器將稽核和登入活動資料傳送至 Azure 儲存體帳戶，即可將它保留超過上述的預設保留期間。 如需詳細資訊，請參閱[將 Azure AD 記錄封存到 Azure 儲存體帳戶](quickstart-azure-monitor-route-logs-to-storage-account.md)。

**安全性信號**

| Report         | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--                 | :--                 |
| 有風險的使用者  | 7 天        | 30 天             | 90 天             |
| 有風險的登入 | 7 天        | 30 天             | 90 天             |

---

### <a name="can-i-see-last-months-data-after-getting-an-azure-ad-premium-license"></a>在取得 Azure AD 進階授權之後，我能否看見上個月的資料？

**否** ，您不能。 Azure 最多可為免費版本的活動資料儲存七天。 這表示，當您從免費切換至 premium 版本時，您最多隻能看見7天的資料。

---
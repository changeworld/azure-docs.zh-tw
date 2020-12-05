---
title: 檢查 Azure Active Directory Domain Services 的健全狀況 |Microsoft Docs
description: 瞭解如何檢查 Azure Active Directory Domain Services (Azure AD DS) 受控網域的健康情況，並使用 Azure 入口網站瞭解狀態訊息。
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 92cc6157c590d3e05b4002e0f071c08e4ec23a16
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620319"
---
# <a name="check-the-health-of-an-azure-active-directory-domain-services-managed-domain"></a>檢查 Azure Active Directory Domain Services 受控網域的健康情況

Azure Active Directory Domain Services (Azure AD DS) 會執行一些背景工作，讓受控網域維持健康狀態並保持最新狀態。 這些工作包括進行備份、套用安全性更新，以及同步處理 Azure AD 的資料。 如果 Azure AD DS 受控網域發生問題，則這些工作可能無法順利完成。 若要檢查並解決任何問題，您可以使用 Azure 入口網站檢查受控網域的健康情況狀態。

本文說明如何查看 Azure AD DS 健全狀況狀態，並瞭解所顯示的資訊或警示。

## <a name="view-the-health-status"></a>查看健全狀況狀態

受控網域的健康情況狀態是使用 Azure 入口網站來查看。 您可以看到上次備份時間和與 Azure AD 同步處理的資訊，以及表示受控網域健康情況問題的任何警示。 若要查看受控網域的健康情況狀態，請完成下列步驟：

1. 在 Azure 入口網站中，搜尋並選取 [Azure AD Domain Services]。
1. 選取您的受控網域，例如 *aaddscontoso.com*。
1. 在 [Azure AD DS 資源] 視窗的左側，選取 [ **健全狀況**]。 下列範例螢幕擷取畫面顯示狀況良好的受控網域，以及上次備份和 Azure AD 同步處理的狀態：

    ![顯示 Azure Active Directory Domain Services 狀態的 Azure 入口網站中的健康情況頁面總覽](./media/check-health/health-page.png)

健康情況頁面的 *上次評估* 時間戳記會顯示上次檢查受控網域的時間。 受控網域的健全狀況會每小時評估一次。 如果您對受控網域進行任何變更，請等候下一個評估週期以查看更新後的健康狀態。

右上方的狀態表示受控網域的整體健全狀況。 狀態會影響網域上所有現有的警示。 下表詳細說明可用的狀態指示器：

| 狀態 | 圖示 | 說明 |
| --- | :----: | --- |
| 執行中 | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15" alt="Green check mark for running"> | 受控網域正常執行，而且沒有任何重大或警告警示。 網域可能會有資訊警示。 |
| 需要注意 (警告)  | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15" alt="Yellow exclamation mark for warning"> | 受控網域上沒有重大警示，但有一或多個警告警示必須解決。 |
| 需要注意 (重要)  | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15" alt="Red exclamation mark for critical"> | 受控網域上有一或多個必須解決的重大警示。 您也可能會有警告和/或資訊警示。 |
| 正在部署 | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15" alt="Blue circular arrows for deploying"> | 正在部署受控網域。 |

## <a name="understand-monitors-and-alerts"></a>瞭解監視和警示

受控網域的健全狀況狀態會顯示兩種類型的資訊- *監視器* 和 *警示*。 監視器會顯示核心背景工作完成的時間。 警示會提供資訊或建議，以改善受控網域的穩定性。

### <a name="monitors"></a>監視器

監視是受控網域中定期檢查的區域。 如果受控網域有任何作用中的警示，可能會導致其中一個監視器回報問題。 Azure AD DS 目前有下欄區域的監視：

* Backup
* 與 Azure AD 的同步處理

#### <a name="backup-monitor"></a>備份監視器

備份監視器會檢查受控網域的自動定期備份是否成功執行。 下表詳細說明可用的備份監視器狀態：

| 詳細資料值 | 說明 |
| --- | --- |
| 從未備份 | 這是新受控網域的正常狀態。 部署受控網域之後，應在24小時內建立第一個備份。 如果此狀態持續發生，請 [開啟 Azure 支援要求][azure-support]。 |
| 上次備份時間是在 1 到 14 天之前 | 此時間範圍是備份監視器的預期狀態。 自動定期備份應該會在這段期間內進行。 |
| 上次備份時間已超過 14 天之前。 | 時間長度超過兩周的 timespan 表示自動定期備份有問題。 作用中的重大警示可能會讓受控網域無法進行備份。 解決受控網域的任何作用中警示。 如果備份監視器未接著更新狀態以報告最近的備份，請 [開啟 Azure 支援要求][azure-support]。 |

#### <a name="synchronization-with-azure-ad-monitor"></a>使用 Azure AD 監視器進行同步處理

受控網域會定期與 Azure Active Directory 同步處理。 使用者和群組物件的數目，以及自上次同步處理之後在 Azure AD 目錄中所做的變更數目，會影響同步處理所花費的時間。 如果受控網域上次在三天前同步處理，請檢查並解決所有作用中的警示。 如果同步處理監視器未在您解決任何作用中警示之後，將狀態更新為顯示最近的同步處理，請 [開啟 Azure 支援要求][azure-support]。

### <a name="alerts"></a>警示

系統會針對受控網域中需要解決的問題產生警示，讓服務能夠正確執行。 每個警示都會說明問題，並提供一個 URL 來概述解決問題的特定步驟。 如需可能的警示及其解決方式的詳細資訊，請參閱 [疑難排解警示](troubleshoot-alerts.md)。

健康狀態警示會分類為下列嚴重性等級：

 * **重大警示** 是嚴重影響受控網域的問題。 應立即解決這些警示。 Azure 平臺無法監視、管理、修補及同步處理受控網域，直到問題解決為止。
 * **警告警示** 會通知您如果問題持續發生，可能會影響受控網域作業的問題。 這些警示也提供保護受控網域的建議。
 * **資訊警示** 是不會對受控網域造成負面影響的通知。 資訊警示可讓您深入瞭解受控網域中發生的情況。

## <a name="next-steps"></a>後續步驟

如需 [健康狀態] 頁面中顯示之警示的詳細資訊，請參閱 [解決受控網域上的警示][troubleshoot-alerts]

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[troubleshoot-alerts]: troubleshoot-alerts.md

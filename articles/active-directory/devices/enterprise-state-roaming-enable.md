---
title: 在 Azure Active Directory 中啟用企業狀態漫遊
description: Windows 裝置中企業狀態漫遊設定的常見問題集。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c44d6266f5ea8cdd4f75d0449cb49852e71c905
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672403"
---
# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>在 Azure Active Directory 中啟用企業狀態漫遊

任何具有 Azure AD Premium 或 Enterprise Mobility + Security (EMS) 授權的組織都可以使用企業狀態漫遊。 如需有關如何取得 Azure AD 訂用帳戶的詳細資訊，請參閱 [Azure AD 產品頁面](https://azure.microsoft.com/services/active-directory)。

當您啟用企業狀態漫遊時，您的組織會自動從 Azure 資訊保護獲得 Azure Rights Management 保護的免費但有使用限制的授權。 此免費訂用帳戶只能加密和解密由企業狀態漫遊所同步的企業設定和應用程式資料。 您必須擁有[付費訂用帳戶](https://azure.microsoft.com/pricing/details/information-protection/)，才能使用 Azure Rights Management 服務的完整功能。

> [!NOTE]
> 本文適用于 2015 年 7 月隨 Windows 10 推出的基於 Windows 10 的基於 Microsoft 邊緣舊版 HTML 的瀏覽器。 本文不適用於 2020 年 1 月 15 日發佈的基於 Microsoft 邊緣鉻的新瀏覽器。 有關新 Microsoft 邊緣的同步行為的詳細資訊，請參閱[文章"Microsoft 邊緣同步](/deployedge/microsoft-edge-enterprise-sync)"。

## <a name="to-enable-enterprise-state-roaming"></a>啟用企業狀態漫遊

1. 登入 [Azure AD 系統管理中心](https://aad.portal.azure.com/)。
1. 選取 [Azure Active Directory]** [裝置]** &gt; ** [企業狀態漫遊]** &gt; ****。
1. 選取 [使用者可以在裝置間同步設定及應用程式資料]****。 如需詳細資訊，請參閱[如何進行裝置設定](/azure/active-directory/device-management-azure-portal)。
  
   ![標示為 [使用者可以在裝置間同步設定及應用程式資料] 的裝置設定影像](./media/enterprise-state-roaming-enable/device-settings.png)
  
若要讓 Windows 10 裝置使用企業狀態漫遊服務，裝置必須使用 Azure AD 身分識別進行驗證。 對於已加入 Azure AD 的裝置，使用者的主要登入身分識別就是其 Azure AD 身分識別，不需要額外設定。 對於使用內部部署 Active Directory 的裝置，IT 管理員必須[設定已加入混合式 Azure Active Directory 的裝置](hybrid-azuread-join-manual-steps.md)。 

## <a name="data-storage"></a>資料儲存體

企業狀態漫遊資料裝載於一或多個 [Azure 區域](https://azure.microsoft.com/regions/)，這些區域最符合 Azure Active Directory 執行個體中設定的國家/區域值。 企業狀態漫遊的資料是根據三個主要地理區域來分割︰北美洲、EMEA 和 APAC。 適用於租用戶的企業狀態漫遊資料是位於本機的地理區域中，並不會跨區域複寫。  例如：

| 國家/區域值 | 將其資料裝載於 |
| -------------------- | ------------------------ |
| EMEA 國家/地區，如法國或尚比亞 | 歐洲內的一個或多個 Azure 區域 |
| 北美國家/地區，如美國或加拿大 | 美國內的一個或多個 Azure 區域 |
| 亞太地區/地區，如澳大利亞或紐西蘭 | 亞洲內的一個或多個 Azure 區域 |
| 南美洲和南極洲區域 | 美國內的一個或多個 Azure 區域 |

國家/地區值是在 Azure AD 目錄建立程序期間所建立，之後無法修改。 如果您需要資料儲存體位置的詳細資料，請向 [Azure 支援](https://azure.microsoft.com/support/options/)提出票證。

## <a name="view-per-user-device-sync-status"></a>檢視每個使用者裝置同步處理狀態

請遵循下列步驟來檢視每個使用者裝置同步處理狀態報告。

1. 登入 [Azure AD 系統管理中心](https://aad.portal.azure.com/)。
1. 選取 [Azure Active Directory]** [使用者]** &gt; ** [所有使用者]** &gt; ****。
1. 選取使用者，然後選取 [裝置]****。
1. 在 [顯示]**** 底下，選取 [裝置同步設定和應用程式資料]****，以顯示同步處理狀態。
  
   ![裝置同步處理資料設定的影像](./media/enterprise-state-roaming-enable/sync-status.png)
  
1. 如果此使用者有裝置在同步處理，您會看到裝置，如下所示。
  
   ![裝置同步處理單欄式資料的影像](./media/enterprise-state-roaming-enable/device-status-row.png)

## <a name="data-retention"></a>資料保留

使用企業狀態漫遊而同步處理至 Microsoft 雲端的資料會保留下來，直到手動刪除或確定該資料已過時為止。 

### <a name="explicit-deletion"></a>明確刪除

明確刪除是指 Azure 管理員刪除使用者或目錄，或是明確要求刪除資料。

* **使用者刪除**：在 Azure AD 中刪除使用者時，使用者帳戶漫遊資料會在 90 至 180 天後刪除。 
* **目錄刪除**：在 Azure AD 中刪除整個目錄是即時作業。 與該目錄相關聯的所有設定資料會在 90 至 180 天後刪除。 
* **依要求刪除**：如果 Azure AD 管理員想要手動刪除特定使用者的資料或設定資料，管理員可以向 [Azure 支援](https://azure.microsoft.com/support/)提出票證。 

### <a name="stale-data-deletion"></a>刪除過時資料

一年 (「保留期限」) 未存取的資料將視為過時，可能會從 Microsoft 雲端中刪除。 保留期限可能有所變更，但不會小於 90 天。 過時的資料可能是一組特定的 Windows/應用程式設定或使用者的所有設定。 例如：

* 如果沒有任何裝置存取特定的設定集合 (例如，從裝置中移除應用程式，或針對使用者的所有裝置停用設定群組，例如「佈景主題」)，則該集合在保留期限之後就會變成過時，可能會被刪除。 
* 如果使用者關閉了其所有設備上的設置同步，則不會訪問任何設置資料，並且該使用者的所有設置資料將過時，並可能在保留期後刪除。 
* 如果 Azure AD 目錄管理員針對整個目錄關閉企業狀態漫遊，則該目錄中的所有使用者會停止同步處理設定，且所有使用者的所有設定資料會變成過時，並且可能在保留期限之後刪除。 

### <a name="deleted-data-recovery"></a>復原已刪除的資料

無法設定資料保留期原則。 一旦永久刪除資料，就無法復原。 但系統只會從 Microsoft 雲端刪除設定資料，而不會從使用者裝置中刪除。 如果任何裝置後來又重新連線至企業狀態漫遊服務，設定就會再次同步處理並儲存在 Microsoft 雲端中。

## <a name="next-steps"></a>後續步驟

* [企業狀態漫遊概觀](enterprise-state-roaming-overview.md)
* [設定和資料漫遊常見問題集](enterprise-state-roaming-faqs.md)
* [設定同步處理的群組原則和 MDM 設定](enterprise-state-roaming-group-policy-settings.md)
* [Windows 10 漫遊設定參考](enterprise-state-roaming-windows-settings-reference.md)
* [疑難排解](enterprise-state-roaming-troubleshooting.md)

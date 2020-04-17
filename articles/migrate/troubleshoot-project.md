---
title: Azure Migrate 專案疑難排解
description: 説明您解決創建和管理 Azure 遷移項目的問題。
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/01/2020
ms.openlocfilehash: b1fc4bce988b13a9ff76fd961d524ce945876054
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535395"
---
# <a name="troubleshoot-azure-migrate-projects"></a>Azure Migrate 專案疑難排解

本文可説明您在創建和管理[Azure 遷移](migrate-services-overview.md)專案時解決問題。

## <a name="how-to-add-new-project"></a>如何添加新專案?

訂閱中可以有多個 Azure 遷移專案。 [瞭解如何](how-to-add-tool-first-time.md)首次創建專案或[添加其他](create-manage-projects.md#create-additional-projects)專案。

## <a name="what-azure-permissions-are-needed"></a>需要哪些 Azure 許可權?

您需要訂閱中的參與者或擁有者許可權才能創建 Azure 遷移專案。

## <a name="cant-find-a-project"></a>找不到項目

尋找現有的 Azure 移轉專案取決於您使用的是目前的版本還是舊版本的 Azure 移轉。 [按照](create-manage-projects.md#find-a-project)。


## <a name="cant-find-a-geography"></a>找不到地理

您可以在[公共和政府](migrate-support-matrix.md#supported-geographies-public-cloud)[雲](migrate-support-matrix.md#supported-geographies-azure-government)受支援的地理位置中創建 Azure 遷移專案。

## <a name="what-are-vm-limits"></a>什麼是 VM 限制?

您可以在單個項目中評估多達 35,000 個 VMware VM 或多達 35,000 台 Hyper-V VM。 專案可以同時包括 VMware VM 和超 V VM,但達到評估限制。

## <a name="can-i-upgrade-old-project"></a>我可以升級舊專案嗎?

無法更新以前版本的 Azure 遷移的專案。 您需要[創建新專案](how-to-add-tool-first-time.md),並加入工具。

## <a name="cant-create-a-project"></a>無法建立項目

如果嘗試建立項目並遇到部署錯誤:

- 請嘗試再次創建專案,以防出現暫時性錯誤。 在 **「部署」中**,按下 **「重新部署**」以重試。
- 檢查訂閱中具有參與者或擁有者許可權。
- 如果要在新添加的地理位置中部署,請稍等片刻,然後重試。
- 如果收到錯誤,"請求必須包含使用者標識標頭",這可能表示您無法訪問組織的 Azure 活動目錄 (Azure AD) 租戶。 在此案例中：
    - 首次添加到 Azure AD 租戶時,會收到加入租戶的電子郵件邀請。
    - 接受要添加到租戶的邀請。
    - 如果您看不到電子郵件,請與有權存取租戶的使用者聯絡,並要求他們[重新向您發送邀請](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users)。
    - 收到邀請電子郵件後,打開它並選擇接受邀請的連結。 然後,註銷 Azure 門戶,然後重新登錄。 (刷新瀏覽器不起作用。然後,您可以開始創建遷移專案。

## <a name="how-do-i-delete-a-project"></a>如何移除項目

[按照這些說明](create-manage-projects.md#delete-a-project)刪除專案。 請注意,刪除專案時,將刪除專案以及有關專案中發現的計算機的元數據。

## <a name="added-tools-dont-show"></a>新增的工具不顯示

請確保您選擇了正確的專案。 在 Azure 遷移中心>**伺服器**或**資料庫中**,按一下螢幕右上角**的「遷移專案(更改)** 旁邊**更改**」。。 選擇正確的訂閱與專案名稱>**確定**。 頁面應使用所選專案的附加工具刷新。

## <a name="next-steps"></a>後續步驟

向 Azure 移轉專案新增[評估](how-to-assess.md)或[移轉](how-to-migrate.md)工具。
---
title: Azure Migrate 專案疑難排解
description: 協助您針對建立和管理 Azure Migrate 專案的問題進行疑難排解。
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/01/2020
ms.openlocfilehash: b1fc4bce988b13a9ff76fd961d524ce945876054
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81535395"
---
# <a name="troubleshoot-azure-migrate-projects"></a>Azure Migrate 專案疑難排解

本文可協助您針對建立和管理[Azure Migrate](migrate-services-overview.md)專案時的問題進行疑難排解。

## <a name="how-to-add-new-project"></a>如何新增專案？

您的訂用帳戶中可以有多個 Azure Migrate 專案。 [瞭解如何](how-to-add-tool-first-time.md)第一次建立專案，或[加入其他](create-manage-projects.md#create-additional-projects)專案。

## <a name="what-azure-permissions-are-needed"></a>需要哪些 Azure 許可權？

您必須有訂用帳戶中的「參與者」或「擁有者」權限，才能建立 Azure Migrate 專案。

## <a name="cant-find-a-project"></a>找不到專案

尋找現有的 Azure Migrate 專案取決於您使用的是目前版本還是舊版的 Azure Migrate。 [請遵循](create-manage-projects.md#find-a-project)。


## <a name="cant-find-a-geography"></a>找不到地理位置

您可以在 [[公用](migrate-support-matrix.md#supported-geographies-public-cloud)] 和 [[政府](migrate-support-matrix.md#supported-geographies-azure-government)] 雲端的支援地理位置中建立 Azure Migrate 專案。

## <a name="what-are-vm-limits"></a>什麼是 VM 限制？

您最多可以在單一專案中評估35000個 VMware Vm 或最多35000個 Hyper-v Vm。 一個專案可以包含 VMware VM 和 Hyper-V VM，數目上限為評量限制。

## <a name="can-i-upgrade-old-project"></a>我可以升級舊的專案嗎？

無法更新舊版 Azure Migrate 的專案。 您需要[建立新的專案](how-to-add-tool-first-time.md)，並在其中加入工具。

## <a name="cant-create-a-project"></a>無法建立專案

如果您嘗試建立專案並遇到部署錯誤：

- 嘗試再次建立專案，以免發生暫時性錯誤。 在 [**部署**] 中，按一下 [**重新部署**] 以再試一次。
- 檢查您擁有訂用帳戶中的參與者或擁有者許可權。
- 如果您要在新加入的地理位置部署，請稍候片刻，然後再試一次。
- 如果您收到錯誤「要求必須包含使用者識別標頭」，這可能表示您無法存取組織的 Azure Active Directory （Azure AD）租使用者。 在此案例中：
    - 當您第一次新增至 Azure AD 租使用者時，您會收到加入租使用者的電子郵件邀請。
    - 接受要新增至租使用者的邀請。
    - 如果您看不到電子郵件，請洽詢具有租使用者存取權的使用者，並要求他們[重新傳送邀請](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users)給您。
    - 收到邀請電子郵件之後，請開啟它，然後選取要接受邀請的連結。 然後，登出 Azure 入口網站並重新登入。 （重新整理瀏覽器將無法正常執行）。然後，您就可以開始建立遷移專案。

## <a name="how-do-i-delete-a-project"></a>如何? 刪除專案

[請遵循這些指示](create-manage-projects.md#delete-a-project)來刪除專案。 請注意，當您刪除專案時，會刪除專案和專案中探索到之電腦的中繼資料。

## <a name="added-tools-dont-show"></a>新增的工具不會顯示

請確定您已選取正確的專案。 在 [Azure Migrate hub >**伺服器**] 或 [**資料庫**] 中，按一下畫面右上角的 [**遷移專案（變更）** ] 旁的 [**變更**]。 選擇正確的訂用帳戶和專案名稱 > **[確定]**。 頁面應該會使用所選取專案的加入工具來重新整理。

## <a name="next-steps"></a>後續步驟

新增[評定](how-to-assess.md)或[遷移](how-to-migrate.md)工具以 Azure Migrate 專案。
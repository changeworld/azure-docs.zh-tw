---
title: 在 Azure 自動化中管理 Vm 的更新和修補程式
description: 本文說明如何使用更新管理來管理 Azure 和非 Azure Vm 的更新和修補程式。
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 07/28/2020
ms.custom: mvc
ms.openlocfilehash: e4f630931e3cd79a46a539955b45d72e5b573271
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2020
ms.locfileid: "87449995"
---
# <a name="manage-updates-and-patches-for-your-vms"></a>管理 Vm 的更新和修補程式

Azure 自動化更新管理中的軟體更新提供一組工具和資源，可協助您管理追蹤和套用軟體更新至 Azure 和混合式雲端中電腦的複雜工作。 有效的軟體更新管理程式是維持營運效率、克服安全性問題，以及降低網路安全性威脅增加風險的必要步驟。 不過，由於科技不斷變動的本質與新型的安全性威脅不斷出現，因此有效的軟體更新管理需要一致且持續的關注。

> [!NOTE]
> 更新管理支援部署第一方更新及預先下載。 這種支援需要在更新的系統上進行變更。 如需了解如何在系統上設定這些設定，請參閱[設定 Windows Update 設定以進行 Azure 自動化更新管理](update-mgmt-configure-wuagent.md)。

嘗試管理 Vm 的更新之前，請確定您已使用下列其中一種方法來啟用它們的更新管理：

* [從自動化帳戶啟用更新管理](update-mgmt-enable-automation-account.md)
* [藉由瀏覽 Azure 入口網站來啟用更新管理](update-mgmt-enable-portal.md)
* [從 Runbook 啟用更新管理](update-mgmt-enable-runbook.md)
* [從 Azure VM 啟用更新管理](update-mgmt-enable-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>限制部署的範圍

更新管理會使用工作區中的範圍設定，來鎖定要接收更新的電腦。 如需詳細資訊，請參閱[限制更新管理部署範圍](update-mgmt-scope-configuration.md)。

## <a name="compliance-assessment"></a>合規性評估

將軟體更新部署至您的電腦之前，請先參閱已啟用機器的更新相容性評估結果。 針對每個軟體更新，會記錄其合規性狀態，然後在評估完成後，會大量收集並轉送以 Azure 監視器記錄。

在 Windows 機器上，合規性掃描預設每 12 小時執行一次。 除了排定的掃描，更新相容性的掃描會在重新開機的 Log Analytics 代理程式的15分鐘內，于更新安裝之前和更新安裝之後起始。 也請務必參閱我們的建議，以瞭解如何使用更新管理[設定 Windows Update 用戶端](update-mgmt-configure-wuagent.md)，以避免任何導致無法正確管理的問題。

針對 Linux 機器，合規性掃描預設每小時執行一次。 如果重新開機 Log Analytics Linux 代理程式，則會在15分鐘內起始合規性掃描。

系統會針對每一部評估的機器，在更新管理中顯示合規性結果。 針對啟用管理的新電腦，最多可能需要30分鐘的時間，儀表板才會顯示更新的資料。

請參閱[監視軟體更新](update-mgmt-view-update-assessments.md)，以瞭解如何查看合規性結果。

## <a name="deploy-updates"></a>部署更新

在檢查相容性結果之後，軟體更新部署階段是部署軟體更新的程式。 若要安裝更新，請將部署排定在發行排程和服務時段之後。 您可以選擇要在部署中包含的更新類型。 例如，您可以包含重大更新或安全性更新，並排除更新彙總套件。

若要瞭解如何排程更新部署，請參閱[部署軟體更新](update-mgmt-deploy-updates.md)。

## <a name="review-update-deployments"></a>審查更新部署

部署完成之後，請檢查此程式，以根據電腦或目標群組來判斷更新部署是否成功。 請參閱[審查部署狀態](update-mgmt-deploy-updates.md#check-deployment-status)以瞭解您可以如何監視部署狀態。

## <a name="next-steps"></a>後續步驟

* 若要瞭解如何建立警示來通知您有關更新部署結果，請參閱[建立更新管理的警示](update-mgmt-configure-alerts.md)。

* 您可以[查詢 Azure 監視器記錄](update-mgmt-query-logs.md)來分析更新評估、部署和其他相關的管理工作。 其中包含預先定義的查詢，可協助您開始著手。
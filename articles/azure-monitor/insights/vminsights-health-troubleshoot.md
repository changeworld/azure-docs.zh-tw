---
title: 針對適用於 VM 的 Azure 監視器 guest health (preview) 進行疑難排解
description: 描述當您有適用於 VM 的 Azure 監視器健康情況問題時，可採取的疑難排解步驟。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/08/2020
ms.openlocfilehash: 2aa83a861697882127c75eb696807f604c3061f0
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184547"
---
# <a name="troubleshoot-azure-monitor-for-vms-guest-health-preview"></a>針對適用於 VM 的 Azure 監視器 guest health (preview) 進行疑難排解
本文說明當您有適用於 VM 的 Azure 監視器健康情況問題時，可採取的疑難排解步驟。

## <a name="error-message-that-no-data-is-available"></a>沒有資料可供使用的錯誤訊息 

![無資料](media/vminsights-health-troubleshoot/no-data.png)


### <a name="verify-that-the-virtual-machine-meets-configuration-requirements"></a>確認虛擬機器符合設定需求

1. 確認虛擬機器是 Azure 虛擬機器。 目前不支援適用於伺服器的 Azure Arc。
2. 確認虛擬機器正在執行 [支援的作業系統](vminsights-health-enable.md?current-limitations.md)。
3. 確認虛擬機器已安裝在 [支援的區域](vminsights-health-enable.md?current-limitations.md)中。
4. 確認 Log Analytics 工作區已安裝在支援的 [區域](vminsights-health-enable.md?current-limitations.md)中。

### <a name="verify-that-the-vm-is-properly-onboarded"></a>確認 VM 已正確上線
確認已成功在虛擬機器上布建 Azure 監視器代理程式擴充功能和來賓 VM 健康情況代理程式。 在 Azure 入口網站中，從虛擬機器的功能表選取 [ **延伸** 模組]。 如果列出兩個代理程式，請參閱 []() 。

![VM 擴充功能](media/vminsights-health-troubleshoot/extensions.png)

### <a name="verify-the-system-assigned-identity-is-enabled-on-the-virtual-machine"></a>確認虛擬機器上已啟用系統指派的身分識別
確認虛擬機器上已啟用系統指派的身分識別。 從 Azure 入口網站的虛擬機器功能表中選取 [身分 **識別** ]。 如果列出兩個代理程式，請參閱 []() 。

![系統指派的身分識別](media/vminsights-health-troubleshoot/system-identity.png)

### <a name="verify-data-collection-rule"></a>確認資料收集規則
確認將健全狀況延伸模組指定為數據源的資料集合規則，與虛擬機器相關聯。

## <a name="error-message-for-bad-request-due-to-insufficient-permissions"></a>因為許可權不足而導致錯誤要求的錯誤訊息
此錯誤表示未在訂用帳戶中註冊 **WorkloadMonitor** 資源提供者。 如需註冊此資源提供者的詳細資訊，請參閱 [Azure 資源提供者和類型](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider) 。 

![不正確的要求](media/vminsights-health-troubleshoot/bad-request.png)

## <a name="next-steps"></a>後續步驟

- [深入瞭解適用於 VM 的 Azure 監視器的來賓健全狀況功能](vminsights-health-overview.md)
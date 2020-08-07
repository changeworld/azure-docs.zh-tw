---
title: Azure App Service 的保留折扣
description: 了解保留折扣如何套用至 Azure App Service 隔離式方案戳記。 折扣會自動套用至區域中的戳記費用。
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 7a4229d60a31d61caf050dabf632c8718ef3c9f3
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87460332"
---
# <a name="how-reservation-discounts-apply-to-azure-app-service-isolated-stamps"></a>保留折扣如何套用至 Azure App Service 隔離式方案戳記

購買 App Service 隔離式方案戳記費用保留容量之後，保留折扣便會自動套用至區域中的戳記費用。 保留折扣會套用至隔離式方案戳記費用計量所發出的使用量。 背景工作角色、額外的前端以及與此戳記相關聯的任何其他資源，則會繼續以一般費率計費。

## <a name="reservation-discount-application"></a>保留折扣的套用

App Service 隔離式方案戳記費用折扣會套用至以小時為單位而執行的隔離式方案戳記。 如果您所部署的戳記未達一小時，則那一小時的保留容量便浪費掉了。 未用完的保留容量無法延續。

購買之後，您購買的保留便會與在特定區域中執行的隔離式方案戳記進行比對。 如果您關閉該戳記，則保留折扣會自動套用至在該區域中執行的任何其他戳記。 沒有任何戳記存在時，保留則會套用至在該區域中建立的下一個戳記。

若戳記未執行滿一小時，保留便會在同一小時內自動套用至相同區域中的其他相符戳記。

## <a name="choose-a-stamp-type---windows-or-linux"></a>選擇戳記類型 - Windows 或 Linux

根據預設，空白的隔離式方案戳記會發出 Windows 戳記計量。 例如，未部署任何背景工作角色時。 部署了 Windows 背景工作角色時，戳記會繼續發出此計量。 如果您部署 Linux 背景工作角色，計量會變更為 Linux 戳記計量。 在同時部署了 Linux 和 Windows 背景工作角色時，戳記會發出 Windows 計量。

因此，戳記計量可能會在戳記的生命週期內，於 Windows 和 Linux 之間變來變去。 同時，保留會專屬於作業系統。 您必須購買保留來支援您打算部署至戳記的背景工作角色。 僅限 Windows 的戳記和混合式戳記會使用 Windows 保留。 只有 Linux 背景工作角色的戳記則會使用 Linux 保留。

當您打算只讓戳記中有 Linux 背景工作角色時，才應該購買 Linux 保留。

## <a name="discount-examples"></a>折扣範例

下列範例會根據部署來顯示隔離式方案戳記費用保留執行個體折扣的套用方式。

- **範例 1**：您在沒有 App Service 隔離式方案戳記的區域中購買一個隔離式方案保留戳記容量執行個體。 您在該區域中部署新的戳記，並支付該戳記的保留費率。
- **範例 2：** 您在已部署 App Service 隔離式方案戳記的區域中購買一個隔離式方案保留戳記容量執行個體。 您開始獲得已部署戳記的保留費率。
- **範例 3**：您在已部署 App Service 隔離式方案戳記的區域中購買一個隔離式方案保留戳記容量執行個體。 您開始在已部署的戳記上獲得保留費率。 之後，您刪除戳記並部署新的戳記。 您獲得新戳記的保留費率。 折扣不會延續至沒有已部署戳記的持續時間。
- **範例 4**：您在某個區域購買一個隔離式方案 Linux 保留戳記容量執行個體，然後將新戳記部署到該區域。 該戳記一開始在部署時並沒有背景工作角色，所以會發出 Windows 戳記計量。 您不會獲得折扣。 當第一個 Linux 背景工作角色部署到戳記上時，其會發出 Linux 戳記計量，並套用保留折扣。 如果之後將 Windows 背景工作角色部署到戳記上，戳記計量便會還原為 Windows。 您也就不會再獲得隔離式方案 Linux 保留戳記保留的折扣。

## <a name="next-steps"></a>後續步驟

- 若要了解如何管理保留項目，請參閱[管理 Azure 保留項目](manage-reserved-vm-instance.md)。
- 若要深入了解如何預先購買 App Service 隔離式方案戳記保留容量以節省成本，請參閱[使用保留容量來預付 Azure App Service 隔離式方案戳記費用](prepay-app-service-isolated-stamp.md)。
- 若要深入了解 Azure 保留項目，請參閱下列文章：
  - [什麼是 Azure 保留項目？](save-compute-costs-reservations.md)
  - [管理 Azure 中的保留](manage-reserved-vm-instance.md)
  - [了解採用隨用隨付費率的訂用帳戶的保留使用量](understand-reserved-instance-usage.md)
  - [了解 Enterprise 註冊的保留項目使用量](understand-reserved-instance-usage-ea.md)

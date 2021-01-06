---
title: 未來 Azure 資訊安全中心的重要變更
description: 您需要留意並提早規劃的各項 Azure 資訊安全中心變更
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2021
ms.author: memildin
ms.openlocfilehash: 0656000a1d6449306e8afe538f846c55c79a31a2
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97915281"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>未來 Azure 資訊安全中心的重要變更

> [!IMPORTANT]
> 本頁中載明的資訊與預先發行產品或功能相關，且內容可能在公開上市之前修改。 Microsoft 對於此處提供的資訊不做任何明示或暗示的承諾或保證。

本頁內容可帶您了解為資訊安全中心規劃的各項變更， 並說明規劃的產品修改，且可能影響您的安全分數或工作流程等項目。

如果要尋找 6 個月之前的版本資訊，請前往[Azure 資訊安全中心的新功能](release-notes.md)。


## <a name="planned-changes"></a>規劃的變更

- [「不適用」資源將在 Azure 原則評量中回報為「符合規範」](#not-applicable-resources-to-be-reported-as-compliant-in-azure-policy-assessments)
- [已新增 35 個預覽建議，以增加 Azure 安全性效能評定的涵蓋範圍](#35-preview-recommendations-being-added-to-increase-coverage-of-azure-security-benchmark)

### <a name="not-applicable-resources-to-be-reported-as-compliant-in-azure-policy-assessments"></a>「不適用」資源將在 Azure 原則評量中回報為「符合規範」

**變更的預估日期：** 2021 年 1 月

目前，針對建議進行評估且識別為 **不適用** 的資源，在 Azure 原則中會顯示為「不符合規範」。 使用者動作無法將其狀態變更為「符合規範」。 在此規劃的變更中，為了讓人更容易理解，這些資源會回報為「符合規範」。

唯一的影響將會出現在 Azure 原則，其中符合規範的資源數目將會增加。 並不會對 Azure 資訊安全中心的安全分數產生影響。

### <a name="35-preview-recommendations-being-added-to-increase-coverage-of-azure-security-benchmark"></a>將新增 35 個預覽建議，以增加 Azure 安全性效能評定的涵蓋範圍

**變更的預估日期：** 2021 年 1 月

Azure 安全性效能評定是 Microsoft 針對以通用合規性架構為基礎的安全性和合規性最佳做法所撰寫的一組 Azure 特定指導方針。 [深入了解 Azure 安全性效能評定](../security/benchmarks/introduction.md)。

下列 35 個預覽建議將新增到 Azure 資訊安全中心，以增加 Azure 安全性效能評定的涵蓋範圍。

預覽建議不會將資源轉譯為「狀況不良」，這些項目也不會納入您安全分數的計算範圍。 您可以盡可能加以補救，以在預覽期間結束時計入您的分數。 若要了解如何回應這些建議，請參閱[Azure 資訊安全中心的補救建議](security-center-remediate-recommendations.md)。

| 安全性控制                     | 新建議                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 啟用待用加密            | - Azure Cosmos DB 帳戶應使用客戶自控金鑰加密待用資料<br>- Azure Machine Learning 工作區應使用客戶自控金鑰 (CMK) 進行加密<br>- 應為 MySQL 伺服器啟用自備金鑰資料保護<br>- 應為 PostgreSQL 伺服器啟用自備金鑰資料保護<br>- 認知服務帳戶應使用客戶自控金鑰 (CMK) 來啟用資料加密<br>- 應使用客戶自控金鑰 (CMK) 進行加密<br>- SQL 受控執行個體應使用客戶自控金鑰來加密待用資料<br>- SQL 伺服器應使用客戶自控金鑰來加密待用資料<br>- 儲存體帳戶應使用客戶自控金鑰 (CMK) 進行加密                                                                                                                                                              |
| 實作安全性最佳做法    | - 訂用帳戶應具有連絡人電子郵件地址以處理安全性問題<br> - 您的訂用帳戶上應啟用 Log Analytics 代理程式的自動化佈建<br> - 應針對高嚴重性警示啟用電子郵件通知<br> - 應已針對高嚴重性警示啟用傳送給訂用帳戶擁有者的電子郵件通知<br> - 金鑰保存庫應啟用清除保護<br> - 金鑰保存庫應已啟用虛刪除 |
| 管理存取權和權限        | - 函數應用程式應啟用「用戶端憑證 (傳入用戶端憑證)」 |
| 保護應用程式防範 DDoS 攻擊 | - 應為應用程式閘道啟用 Web 應用程式防火牆 (WAF)<br> - 應為 Azure Front Door Service 啟用 Web 應用程式防火牆 (WAF) |
| 限制未經授權的網路存取 | - 應該在 Key Vault 上啟用防火牆<br> - 應設定 Key Vault 的私人端點<br> - 應用程式設定應使用私人連結<br> - Azure Cache for Redis 應位於虛擬網路內<br> - Azure 事件方格網域應使用私人連結<br> - Azure 事件方格主題應使用私人連結<br> - Azure Machine Learning 工作區應使用私人連結<br> - Azure SignalR Service 應使用私人連結<br> - Azure Spring Cloud 應使用網路插入<br> - 不應允許不受限制的網路存取<br> - 容器登錄應使用私人連結<br> - 應為 MariaDB 伺服器停用公用網路存取<br> - 應為 MySQL 伺服器停用公用網路存取<br> - 應為 PostgreSQL 伺服器停用公用網路存取<br> - 儲存體帳戶應使用私人連結連線<br> - 儲存體帳戶應使用虛擬網路規則來限制網路存取<br> - VM 映像產生器範本應使用私人連結|
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

相關連結：

- [深入了解 Azure 安全性效能評定](../security/benchmarks/introduction.md)
- [深入了解適用於 MariaDB 的 Azure 資料庫](../mariadb/overview.md)
- [深入了解適用於 MySQL 的 Azure 資料庫](../mysql/overview.md)
- [深入了解適用於 PostgreSQL 的 Azure 資料庫](../postgresql/overview.md)





## <a name="next-steps"></a>後續步驟

如需產品的所有最近變更，請參閱 [Azure 資訊安全中心有哪些新功能？](release-notes.md)。
---
title: AKS 虛擬機器主機中的安全強化
description: 瞭解 AKS VM 主機作業系統中的安全強化
services: container-service
author: saudas
ms.topic: article
ms.date: 09/11/2019
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: d4105a9fba3c40c563198040afb811625727ead0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594375"
---
# <a name="security-hardening-in-aks-virtual-machine-hosts"></a>AKS 虛擬機器主機中的安全強化 

Azure 庫伯奈斯服務 （AKS） 是符合 SOC、ISO、PCI DSS 和 HIPAA 標準的安全服務。 本文介紹了應用於 AKS 虛擬機器主機的安全強化。 有關 AKS 安全性的詳細資訊，請參閱[Azure 庫伯奈斯服務 （AKS） 中應用程式和群集的安全概念](https://docs.microsoft.com/azure/aks/concepts-security)。

AKS 群集部署在主機虛擬機器上，這些虛擬機器運行安全優化的作業系統。 此主機作業系統當前基於 Ubuntu 16.04.LTS 映射，並應用了一組額外的安全強化步驟（請參閱安全強化詳細資訊）。   

安全強化主機作業系統的目標是減少攻擊的表面區域，並允許以安全的方式部署容器。 

> [!Important]
> 安全強化作業系統不是 CIS 基準作業系統。 雖然與獨聯體基準有重疊，但目標不是遵守獨聯體標準。 主機作業系統強化的目標是與 Microsoft 自己的內部主機安全標準一致的安全級別相融合。 

## <a name="security-hardening-features"></a>安全強化功能 

* 預設情況下，AKS 提供安全優化的主機作業系統。 當前沒有選擇備用作業系統的選項。 

* Azure 將每日修補程式（包括安全修補程式）應用於 AKS 虛擬機器主機。 其中一些修補程式需要重新開機，而其他修補程式則不需要重新開機。 您負責根據需要安排 AKS VM 主機重新開機。 有關如何自動進行 AKS 修補的指導，請參閱[修補 AKS 節點](https://docs.microsoft.com/azure/aks/node-updates-kured)。

以下是在 AKS-Engine 中實現的圖像強化工作的摘要，該工作用於生成安全優化的主機作業系統。 這項工作[是在此 GitHub 專案中](https://github.com/Azure/aks-engine/projects/7)實現的。  

AKS-Engine 目前不會推廣或遵守任何特定的安全標準，但提供 CIS（互聯網安全中心）審核的專用 ID（如果適用）時是為方便起見。 

## <a name="whats-configured"></a>配置了什麼？

| Cis  | 審計描述| 
|---|---|
| 1.1.1.1 |確保禁用 cramfs 檔案系統安裝|
| 1.1.1.2 |確保禁用自由 vxfs 檔案系統安裝|
| 1.1.1.3 |確保禁用 jffs2 檔案系統的安裝|
| 1.1.1.4 |確保禁用 HFS 檔案系統安裝|
| 1.1.1.5 |確保禁用 HFS Plus 檔案系統安裝|
|1.4.3 |確保單使用者模式所需的身份驗證 |
|1.7.1.2 |確保正確配置本地登錄警告橫幅 |
|1.7.1.3 |確保正確配置遠端登入警告橫幅 |
|1.7.1.5 |確保配置 /etc/問題的許可權 |
|1.7.1.6 |確保配置 /etc/問題.net 的許可權 |
|2.1.5 |確保 --流式連接空閒超時未設置為 0 |
|3.1.2 |確保禁用資料包重定向發送 |
|3.2.1 |確保不接受源路由包 |
|3.2.2 |確保不接受 ICMP 重定向 |
|3.2.3 |確保不接受安全的 ICMP 重定向 |
|3.2.4 |確保記錄可疑資料包 |
|3.3.1 |確保不接受 IPv6 路由器通告 |
|3.5.1 |確保禁用 DCCP |
|3.5.2 |確保 SCTP 已禁用 |
|3.5.3 |確保禁用 RDS |
|3.5.4 |確保禁用 TIPC |
|4.2.1.2 |確保配置了日誌記錄 |
|5.1.2 |確保配置 /etc/crontab 上的許可權 |
|5.2.4 |確保 SSH X11 轉發已禁用 |
|5.2.5 |確保 SSH MaxAuthTries 設置為 4 或更少 |
|5.2.8 |確保禁用 SSH 根登錄 |
|5.2.10 |確保 SSH 許可證使用者環境已禁用 |
|5.2.11 |確保僅使用經批准的 MAX 演算法 |
|5.2.12 |確保配置 SSH 空閒超時間隔 |
|5.2.13 |確保 SSH 登錄寬限期設置為一分鐘或更少 |
|5.2.15 |確保配置 SSH 警告橫幅 |
|5.3.1 |確保配置密碼創建要求 |
|5.4.1.1 |確保密碼過期 90 天或更少 |
|5.4.1.4 |確保非活動密碼鎖為 30 天或更少 |
|5.4.4 |確保預設使用者 umask 是 027 或更嚴格的 |
|5.6 |確保限制對 su 命令的訪問|

## <a name="additional-notes"></a>其他注意事項
 
* 為了進一步減少攻擊表面積，作業系統中禁用了一些不必要的內核模組驅動程式。 

* 在 AKS 平臺之外不支援安全強化的作業系統。 

## <a name="next-steps"></a>後續步驟  

有關 AKS 安全性的詳細資訊，請參閱以下文章： 

[Azure 庫伯奈斯服務 （AKS）](https://docs.microsoft.com/azure/aks/intro-kubernetes)

[AKS 安全注意事項](https://docs.microsoft.com/azure/aks/concepts-security)

[AKS 最佳實踐](https://docs.microsoft.com/azure/aks/best-practices)

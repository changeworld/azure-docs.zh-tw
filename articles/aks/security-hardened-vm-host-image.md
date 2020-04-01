---
title: AKS 虛擬機器主機中的安全強化
description: 瞭解 AKS VM 主機作業系統中的安全強化
services: container-service
author: mlearned
ms.topic: article
ms.date: 09/11/2019
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: b7552fc083c5ed340dc54c2a31160b0c8b4bd076
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420909"
---
# <a name="security-hardening-for-aks-agent-node-host-os"></a>AKS 代理節點主機作業系統的安全強化

Azure 庫伯奈斯服務 (AKS) 是符合 SOC、ISO、PCI DSS 和 HIPAA 標準的安全服務。 本文介紹了應用於 AKS 虛擬機主機的安全強化。 有關 AKS 安全性的詳細資訊,請參閱[Azure 庫伯奈斯服務 (AKS) 中應用程式和叢集的安全概念](https://docs.microsoft.com/azure/aks/concepts-security)。

> [!Note]
> 本文檔僅限定在 AKS 中的 Linux 代理。

AKS 群集部署在主機虛擬機器上,這些虛擬機器運行安全優化的作業系統,用於在 AKS 上執行的容器。 此主機作業系統基於**Ubuntu 16.04.LTS**映射,並應用了額外的安全強化和優化(請參閱安全強化詳細資訊)。

安全強化主機操作系統的目標是減少攻擊的表面區域,並優化以安全的方式部署容器。

> [!Important]
> 安全強化作業系統不是 CIS 基準作業系統。 雖然與獨聯體基準有重疊,但目標不是遵守獨聯體標準。 主機作業系統強化的目標是與 Microsoft 自己的內部主機安全標準一致的安全級別相融合。

## <a name="security-hardening-features"></a>安全強化功能

* 默認情況下,AKS 提供安全優化的主機作業系統。 沒有選擇備用作業系統的選項。

* Azure 將每日修補程式(包括安全修補程式)應用於 AKS 虛擬機主機。 其中一些修補程式需要重新啟動,而其他修補程式則不需要重新啟動。 您負責根據需要安排 AKS VM 主機重新啟動。 有關如何自動進行 AKS 修補的指導,請參閱[修補 AKS 節點](https://docs.microsoft.com/azure/aks/node-updates-kured)。

## <a name="what-is-configured"></a>設定的內容

| Cis  | 稽核描述|
|---|---|
| 1.1.1.1 |確保關閉 cramfs 檔案系統安裝|
| 1.1.1.2 |確保關閉自由 vxfs 檔案系統安裝|
| 1.1.1.3 |確保關閉 jffs2 檔案系統的安裝|
| 1.1.1.4 |確保關閉 HFS 檔案系統安裝|
| 1.1.1.5 |確保關閉 HFS Plus 檔案系統安裝|
|1.4.3 |確保單使用者模式所需的身分驗證 |
|1.7.1.2 |確保正確設定本地登入警告橫幅 |
|1.7.1.3 |確保正確設定遠端登入警告橫幅 |
|1.7.1.5 |確保設定 /etc/問題的權限 |
|1.7.1.6 |確保設定 /etc/問題.net 的權限 |
|2.1.5 |確保 --串流式連接空閒逾時未設定為 0 |
|3.1.2 |確保停用封包重定傳送 |
|3.2.1 |確保不接受源路由包 |
|3.2.2 |確保不接受 ICMP 重定向 |
|3.2.3 |確保不接受安全的 ICMP 重定向 |
|3.2.4 |確保紀錄可疑封包 |
|3.3.1 |確保不接受 IPv6 路由器通告 |
|3.5.1 |確保關閉 DCCP |
|3.5.2 |確保 SCTP 已關閉 |
|3.5.3 |確保關閉 RDS |
|3.5.4 |確保關閉 TIPC |
|4.2.1.2 |確保設定紀錄記錄 |
|5.1.2 |確保設定 /etc/crontab 上的權限 |
|5.2.4 |確保 SSH X11 轉寄已關閉 |
|5.2.5 |確保 SSH MaxAuthTries 設定為 4 或更少 |
|5.2.8 |確保關閉 SSH 根登入 |
|5.2.10 |確保 SSH 授權使用者環境已關閉 |
|5.2.11 |確保僅使用經批准的 MAX 演演算法 |
|5.2.12 |確保設定 SSH 閒置逾時間隔 |
|5.2.13 |確保 SSH 登錄寬限期設置為一分鐘或更少 |
|5.2.15 |確保設定 SSH 警告橫幅 |
|5.3.1 |確保設定密碼建立要求 |
|5.4.1.1 |確保密碼過期 90 天或更少 |
|5.4.1.4 |確保非作用密碼鎖為 30 天或更少 |
|5.4.4 |確保預設使用者 umask 是 027 或更嚴格的 |
|5.6 |確保限制對 su 命令的存取|

## <a name="additional-notes"></a>其他注意事項
 
* 為了進一步減少攻擊表面積,操作系統中禁用了一些不必要的內核模組驅動程式。

* 安全強化作業系統專為 AKS 構建和維護,不支援 AKS 平臺之外。

## <a name="next-steps"></a>後續步驟  

有關 AKS 安全性的詳細資訊,請參閱以下文章: 

[Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/intro-kubernetes)

[AKS 安全注意事項](https://docs.microsoft.com/azure/aks/concepts-security)

[AKS 最佳實作](https://docs.microsoft.com/azure/aks/best-practices)

---
title: AKS 虛擬機器主機中的安全性強化
description: 瞭解 AKS VM 主機 OS 中的安全性強化
services: container-service
author: mlearned
ms.topic: article
ms.date: 09/11/2019
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: b7552fc083c5ed340dc54c2a31160b0c8b4bd076
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80420909"
---
# <a name="security-hardening-for-aks-agent-node-host-os"></a>AKS 代理程式節點主機 OS 的安全性強化

Azure Kubernetes Service （AKS）是與 SOC、ISO、PCI DSS 和 HIPAA 標準相容的安全服務。 本文涵蓋適用于 AKS 虛擬機器主機的安全性強化。 如需 AKS 安全性的詳細資訊，請參閱[Azure Kubernetes Service （AKS）中應用程式和叢集的安全性概念](https://docs.microsoft.com/azure/aks/concepts-security)。

> [!Note]
> 本檔的範圍僅限於 AKS 中的 Linux 代理程式。

AKS 叢集會部署在主機虛擬機器上，其會執行安全性優化 OS，以供在 AKS 上執行的容器使用。 此主機 OS 是以**Ubuntu 16.04. LTS**映射為基礎，並套用額外的安全性強化和優化（請參閱安全性強化詳細資料）。

安全性強化主機 OS 的目標是要減少攻擊的介面區，並以安全的方式將容器的部署優化。

> [!Important]
> 安全性強化的 OS 不是 CIS 的基準。 雖然與 CIS 基準重迭，但目標並不是 CIS 相容。 主機作業系統強化的目標是要與 Microsoft 自己的內部主機安全性標準一致的安全性層級。

## <a name="security-hardening-features"></a>安全性強化功能

* AKS 預設會提供安全性優化主機 OS。 沒有選擇替代作業系統的選項。

* Azure 會將每日修補程式（包括安全性修補程式）套用至 AKS 虛擬機器主機。 其中有些修補程式需要重新開機，有些則不會。 您必須負責排程 AKS VM 主機重新開機（如有需要）。 如需如何將 AKS 修補自動化的指引，請參閱[修補 AKS 節點](https://docs.microsoft.com/azure/aks/node-updates-kured)。

## <a name="what-is-configured"></a>設定的內容

| CIS  | Audit 描述|
|---|---|
| 1.1.1.1 |確定已停用 cramfs 檔案系統的裝載|
| 1.1.1.2 |確定已停用 freevxfs 檔案系統的裝載|
| 1.1.1.3 |確定已停用 jffs2 檔案系統的裝載|
| 1.1.1.4 |確定已停用 HFS 檔案系統的裝載|
| 1.1.1.5 |確定已停用 HFS Plus 檔案系統的裝載|
|1.4.3 |確保單一使用者模式需要驗證 |
|1.7.1.2 |確定已正確設定本機登入警告橫幅 |
|1.7.1.3 |確定已正確設定遠端登入警告橫幅 |
|1.7.1.5 |確認已設定/etc/issue 的許可權 |
|1.7.1.6 |確認已設定/etc/issue.net 的許可權 |
|2.1.5 |確認--串流-連線-閒置-timeout 未設定為0 |
|3.1.2 |確定已停用封包重新導向傳送 |
|3.2.1 |請確定不接受來源路由套件 |
|3.2.2 |確定不接受 ICMP 重新導向 |
|3.2.3 |確保不接受安全的 ICMP 重新導向 |
|3.2.4 |確定已記錄可疑的封包 |
|3.3.1 |確定不接受 IPv6 路由器公告 |
|3.5.1 |確定已停用 DCCP |
|3.5.2 |確定已停用 SCTP |
|3.5.3 |確定已停用 RDS |
|3.5.4 |確定已停用 TIPC |
|4.2.1.2 |確定已設定記錄 |
|5.1.2 |確認已設定/etc/crontab 的許可權 |
|5.2.4 |確定已停用 SSH X11 轉送 |
|5.2.5 |確定 SSH MaxAuthTries 設定為4或更少 |
|第5.2.8 |確認 SSH 根登入已停用 |
|第5.2.10 |確定 SSH PermitUserEnvironment 已停用 |
|第5.2.11 |請確定只使用已核准的最大演算法 |
|第5.2.12 |確保已設定 SSH 閒置逾時間隔 |
|5.2.13 |確定 SSH LoginGraceTime 設定為一分鐘或更少 |
|第5.2.15 |確定已設定 SSH 警告橫幅 |
|5.3.1 |確定已設定密碼建立需求 |
|5.4.1.1 |確認密碼到期時間為90天或更少 |
|5.4.1.4 |確保非使用中的密碼鎖定為30天或更少 |
|5.4.4 |確保預設使用者 umask 為027或更嚴格的限制 |
|5.6 |確保對 su 命令的存取受到限制|

## <a name="additional-notes"></a>其他注意事項
 
* 為了進一步減少受攻擊面，作業系統中已停用一些不必要的核心模組驅動程式。

* 安全性強化的作業系統是專為 AKS 而建立和維護，在 AKS 平臺之外不受支援。

## <a name="next-steps"></a>後續步驟  

如需 AKS 安全性的詳細資訊，請參閱下列文章： 

[Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/intro-kubernetes)

[AKS 安全性考慮](https://docs.microsoft.com/azure/aks/concepts-security)

[AKS 最佳作法](https://docs.microsoft.com/azure/aks/best-practices)

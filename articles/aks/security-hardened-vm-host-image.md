---
title: AKS 虛擬機器主機中的安全性強化
description: 瞭解 AKS VM 主機 OS 中的安全性強化
services: container-service
author: saudas
ms.topic: article
ms.date: 09/11/2019
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: d4105a9fba3c40c563198040afb811625727ead0
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77594375"
---
# <a name="security-hardening-in-aks-virtual-machine-hosts"></a>AKS 虛擬機器主機中的安全性強化 

Azure Kubernetes Service （AKS）是與 SOC、ISO、PCI DSS 和 HIPAA 標準相容的安全服務。 本文涵蓋適用于 AKS 虛擬機器主機的安全性強化。 如需 AKS 安全性的詳細資訊，請參閱[Azure Kubernetes Service （AKS）中應用程式和叢集的安全性概念](https://docs.microsoft.com/azure/aks/concepts-security)。

AKS 叢集會部署在主機虛擬機器上，以執行安全性優化的作業系統。 此主機 OS 目前以 Ubuntu 16.04. LTS 映射為基礎，其中已套用一組額外的安全性強化步驟（請參閱安全性強化詳細資料）。   

安全性強化主機 OS 的目標是要減少攻擊的介面區，並允許以安全的方式部署容器。 

> [!Important]
> 安全性強化的 OS 不是 CIS 的基準。 雖然與 CIS 基準重迭，但目標並不是 CIS 相容。 主機作業系統強化的目標是要與 Microsoft 自己的內部主機安全性標準一致的安全性層級。 

## <a name="security-hardening-features"></a>安全性強化功能 

* AKS 預設會提供安全性優化主機 OS。 目前沒有選擇替代作業系統的選項。 

* Azure 會將每日修補程式（包括安全性修補程式）套用至 AKS 虛擬機器主機。 其中有些修補程式需要重新開機，有些則不會。 您必須負責排程 AKS VM 主機重新開機（如有需要）。 如需如何將 AKS 修補自動化的指引，請參閱[修補 AKS 節點](https://docs.microsoft.com/azure/aks/node-updates-kured)。

以下摘要說明在 AKS 引擎中執行的映射強化工作，以產生安全性優化主機 OS。 已[在此 GitHub 專案中執行此](https://github.com/Azure/aks-engine/projects/7)工作。  

AKS 引擎目前不會升階或遵守任何特定的安全性標準，但在適用的情況下，會為您提供 CIS （Center for Internet Security） audit Id。 

## <a name="whats-configured"></a>設定的內容為何？

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

* 在 AKS 平臺之外，不支援安全強化的作業系統。 

## <a name="next-steps"></a>後續步驟  

如需 AKS 安全性的詳細資訊，請參閱下列文章： 

[Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/intro-kubernetes)

[AKS 安全性考慮](https://docs.microsoft.com/azure/aks/concepts-security)

[AKS 最佳作法](https://docs.microsoft.com/azure/aks/best-practices)

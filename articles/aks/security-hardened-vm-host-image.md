---
title: AKS 虛擬機器主機中的安全性強化
description: 瞭解 AKS VM 主機 OS 中的安全性強化
services: container-service
author: mlearned
ms.topic: article
ms.date: 09/11/2019
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 84b826ce33b5395db5bd38e883b3a0fb3425725b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86244033"
---
# <a name="security-hardening-for-aks-agent-node-host-os"></a>AKS 代理程式節點主機 OS 的安全性強化

Azure Kubernetes Service (AKS) 是與 SOC、ISO、PCI DSS 和 HIPAA 標準相容的安全服務。 本文涵蓋適用于 AKS 虛擬機器主機的安全性強化。 如需 AKS 安全性的詳細資訊，請參閱 [Azure Kubernetes Service (AKS) 中應用程式和叢集的安全性概念 ](./concepts-security.md)。

> [!Note]
> 本檔的範圍僅限於 AKS 中的 Linux 代理程式。

AKS 叢集會部署在主機虛擬機器上，其會執行安全性優化作業系統，以供在 AKS 上執行的容器使用。 此主機 OS 是以 **Ubuntu 16.04 LTS** 映射為基礎，並套用額外的安全性強化和優化 (請參閱安全性強化詳細資料) 。

安全性強化主機 OS 的目標是要減少攻擊的介面區，並以安全的方式將容器的部署優化。

> [!Important]
> 安全性強化的作業系統不是 CIS 的基準。 雖然與 CIS 效能評定重迭，但目標並不符合 CIS 規範。 主機作業系統強化的目標是要專注于與 Microsoft 本身的內部主機安全性標準一致的安全性層級。

## <a name="security-hardening-features"></a>安全性強化功能

* AKS 預設會提供安全性優化主機 OS。 沒有選取其他作業系統的選項。

* Azure 會套用每日修補程式 (包括) 至 AKS 虛擬機器主機的安全性修補程式。 其中有些修補程式需要重新開機，其他修補程式則不需要重新開機。 您必須視需要負責排程 AKS VM 主機重新開機。 如需如何將 AKS 修補自動化的指引，請參閱 [修補 AKS 節點](./node-updates-kured.md)。

## <a name="what-is-configured"></a>設定的內容

| Cis  | 審核描述|
|---|---|
| 1.1.1.1 |確定已停用裝載 cramfs 檔案系統|
| 1.1.1.2 |確定已停用裝載 freevxfs 檔案系統|
| 1.1.1.3 |確定已停用裝載 jffs2 檔案系統|
| 1.1.1.4 |確定已停用裝載 HFS 檔案系統|
| 1.1.1.5 |確定已停用裝載 HFS Plus 檔案系統|
|1.4.3 |確定單一使用者模式需要驗證 |
|1.7.1.2 |確定已正確設定本機登入警告橫幅 |
|1.7.1.3 |確定已正確設定遠端登入警告橫幅 |
|1.7.1.5 |確定已設定/etc/issue 的許可權 |
|1.7.1.6 |確定已設定/etc/issue.net 的許可權 |
|2.1.5 |確定--------閒置-timeout 未設定為0 |
|3.1.2 |確定已停用封包重新導向傳送 |
|3.2.1 |確定不接受來源路由套件 |
|3.2.2 |確定不接受 ICMP 重新導向 |
|3.2.3 |確定不接受安全的 ICMP 重新導向 |
|3.2.4 |確定已記錄可疑的封包 |
|3.3.1 |確定不接受 IPv6 路由器公告 |
|3.5.1 |確定 DCCP 已停用 |
|3.5.2 |確定已停用 SCTP |
|3.5.3 |確定已停用 RDS |
|3.5.4 |確定 TIPC 已停用 |
|4.2.1.2 |確定已設定記錄 |
|5.1.2 |確定已設定/etc/crontab 的許可權 |
|5.2.4 |確定已停用 SSH X11 轉送 |
|5.2.5 |確定 SSH MaxAuthTries 設定為4或更低 |
|5.2.8 |確定 SSH 根登入已停用 |
|第5.2.10 |確定已停用 SSH PermitUserEnvironment |
|第5.2.11 |請確定只使用已核准的最大演算法 |
|第5.2.12 |確定已設定 SSH 閒置逾時間隔 |
|5.2.13 |確定 SSH LoginGraceTime 設定為一分鐘或更短 |
|第5.2.15 |確定已設定 SSH 警告橫幅 |
|5.3.1 |確定已設定密碼建立需求 |
|5.4.1.1 |確認密碼到期期限為90天或更少 |
|5.4.1.4 |確定非使用中的密碼鎖定為30天或更少 |
|5.4.4 |確定預設的使用者 umask 是027或更嚴格的 |
|5.6 |確定對 su 命令的存取受到限制|

## <a name="additional-notes"></a>其他注意事項
 
* 為了進一步減少受攻擊面區，作業系統中已停用一些不必要的核心模組驅動程式。

* 安全性強化的作業系統是專門針對 AKS 所建立和維護，而且在 AKS 平臺以外不受支援。

## <a name="next-steps"></a>接下來的步驟  

如需 AKS 安全性的詳細資訊，請參閱下列文章： 

[Azure Kubernetes Service (AKS)](./intro-kubernetes.md)

[AKS 安全性考慮 ](./concepts-security.md)

[AKS 最佳作法 ](./best-practices.md)

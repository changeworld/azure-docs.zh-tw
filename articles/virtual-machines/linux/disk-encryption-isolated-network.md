---
title: 隔離網路上的 Azure 磁片加密
description: 本文為 Linux VM 提供了 Microsoft Azure 磁片加密的故障排除提示。
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 02/27/2020
ms.custom: seodec18
ms.openlocfilehash: aa0dc204a017e2d40eb3952a9ede0755127f8de2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970668"
---
# <a name="azure-disk-encryption-on-an-isolated-network"></a>隔離網路上的 Azure 磁片加密

當連線能力受防火牆、Proxy 需求或網路安全性群組 (NSG) 設定限制時，擴充功能執行所需工作的能力可能會受到中斷。 這個中斷會產生狀態訊息 (例如「無法在 VM 上使用擴充功能狀態」)。

## <a name="package-management"></a>套件管理

Azure 磁片加密取決於許多元件，這些元件通常作為 ADE 啟用的一部分安裝（如果不存在）。 當防火牆後面或與 Internet 隔離時，這些包必須預先安裝或在本地可用。

以下是每個分發所需的包。 有關支援的發行版本和卷類型的完整清單，請參閱[支援的 VM 和作業系統](disk-encryption-overview.md#supported-vms-and-operating-systems)。

- **Ubuntu 14.04， 16.04， 18.04**： lsscsi， psmisc， 在， 墓穴設置箱， 蛇部分， 蛇六， 丙沙
- **CentOS 7.2 - 7.7**： lsscsi， psmisc， lvm2， uuid， 在， 補丁， 密碼設置， 加密設定-重新加密， 分塊， procps-ng， util-linux
- **CentOS 6.8**： lsscsi， psmisc， lvm2， uuid， 在， 加密- 重新加密， 分塊， 蛇六
- **RedHat 7.2 - 7.7**： lsscsi， psmisc， lvm2， uuid， 在， 補丁， 密碼設置， 加密設定-重新加密， procps-ng， util-linux
- **RedHat 6.8**： lsscsi， psmisc， lvm2， uuid， 在， 補丁， 加密- 重新加密
- **開SUSE 42.3， SLES 12-SP4， 12-SP3**： lsscsi， 密碼設置

在 Red Hat 上，在需要 Proxy 時，您必須確保已正確地設定您的訂用帳戶管理員和 Yum。 如需詳細資訊，請參閱[如何針對訂用帳戶管理員和 Yum 問題進行疑難排解](https://access.redhat.com/solutions/189533)。  

手動安裝套裝軟體時，還必須在發佈新版本時手動升級它們。

## <a name="network-security-groups"></a>網路安全性群組
任何套用的網路安全性群組設定仍然必須允許端點，從而符合磁碟加密的記載網路設定必要條件。  請參閱[Azure 磁片加密：網路要求](disk-encryption-overview.md#networking-requirements)

## <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure 磁片加密與 Azure AD（早期版本）

如果將[Azure 磁片加密與 Azure AD（早期版本）一起使用](disk-encryption-overview-aad.md)，則需要為所有發行版本手動安裝[Azure 活動目錄庫](../../active-directory/azuread-dev/active-directory-authentication-libraries.md)（除了適用于發行版本的包外，[如上所述](#package-management)）。

使用 [Azure AD 認證](disk-encryption-linux-aad.md)啟用加密時，目標 VM 必須允許連線到 Azure Active Directory 端點和金鑰保存庫端點。 目前的 Azure Active Directory 驗證端點列在 [Office 365 URL 與 IP 位址範圍](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges)文件的第 56 和 59 節。 如需金鑰保存庫的指示，請參閱文件中的如何[在防火牆後存取 Azure 金鑰保存庫](../../key-vault/key-vault-access-behind-firewall.md)。

### <a name="azure-instance-metadata-service"></a>Azure 執行個體中繼資料服務 

虛擬機器必須能夠訪問[Azure 實例中繼資料服務](instance-metadata-service.md)終結點，該終結點使用僅可在 VM 內訪問的已知不可路由 IP`169.254.169.254`位址 （ ） 。  不支援將本地 HTTP 流量更改到此位址的代理配置（例如，添加 X 轉發-for 標頭）。

## <a name="next-steps"></a>後續步驟

- 查看 Azure[磁片加密故障排除](disk-encryption-troubleshooting.md)的更多步驟
- [待用 Azure 資料加密](../../security/fundamentals/encryption-atrest.md)

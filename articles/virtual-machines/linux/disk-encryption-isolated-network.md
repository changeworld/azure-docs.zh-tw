---
title: 隔離網路上的 Azure 磁碟加密
description: 在本文中，瞭解 Linux Vm 上 Microsoft Azure 磁片加密的疑難排解秘訣。
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 02/27/2020
ms.custom: seodec18
ms.openlocfilehash: 1157a18954c30e1f1d93aa33dbea6104491b5353
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2020
ms.locfileid: "91396446"
---
# <a name="azure-disk-encryption-on-an-isolated-network"></a>隔離網路上的 Azure 磁碟加密

當連線能力受防火牆、Proxy 需求或網路安全性群組 (NSG) 設定限制時，擴充功能執行所需工作的能力可能會受到中斷。 這個中斷會產生狀態訊息 (例如「無法在 VM 上使用擴充功能狀態」)。

## <a name="package-management"></a>套件管理

Azure 磁碟加密取決於一些元件，如果尚未存在，這些元件通常會安裝為 ADE 啟用的一部分。 在防火牆後方或獨立于網際網路的情況下，必須在本機預先安裝或使用這些套件。

以下是每個散發所需的套件。 如需支援的散發版本和磁片區類型的完整清單，請參閱 [支援的 vm 和作業系統](disk-encryption-overview.md#supported-vms-and-operating-systems)。

- **Ubuntu 14.04、16.04、18.04**： lsscsi、psmisc、at、cryptsetup-bin、python-parted、python-6、procps
- **CentOS 7.2-7.7**： lsscsi、psmisc、lvm2、uuid、at、patch、cryptsetup、cryptsetup-重新加密、pyparted、procps-ng、util-linux
- **CentOS 6.8**： lsscsi、psmisc、lvm2、uuid、at、cryptsetup-重新加密、pyparted、python-六
- **RedHat 7.2-7.7**： lsscsi、psmisc、lvm2、uuid、at、patch、cryptsetup、cryptsetup-重新加密、procps-ng、util-linux
- **RedHat 6.8**： lsscsi、psmisc、lvm2、uuid、at、patch、cryptsetup-重新加密
- **openSUSE 42.3、SLES 12-SP4、12-SP3**： lsscsi、cryptsetup

在 Red Hat 上，在需要 Proxy 時，您必須確保已正確地設定您的訂用帳戶管理員和 Yum。 如需詳細資訊，請參閱[如何針對訂用帳戶管理員和 Yum 問題進行疑難排解](https://access.redhat.com/solutions/189533)。  

當套件以手動方式安裝時，也必須在發行新版本時以手動方式升級。

## <a name="network-security-groups"></a>網路安全性群組
任何套用的網路安全性群組設定仍然必須允許端點，從而符合磁碟加密的記載網路設定必要條件。  請參閱 [Azure 磁碟加密：網路需求](disk-encryption-overview.md#networking-requirements)

## <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>使用 Azure AD (舊版 Azure 磁碟加密) 

如果搭配使用 [Azure 磁碟加密與 Azure AD (舊版) ](disk-encryption-overview-aad.md)，除了適用于發行版本的套件之外，您還需要針對所有散發版本 Azure Active Directory 手動安裝 [ (程式庫](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) ， [如上](#package-management) 所示) 。

使用 [Azure AD 認證](disk-encryption-linux-aad.md)啟用加密時，目標 VM 必須允許連線到 Azure Active Directory 端點和金鑰保存庫端點。 目前的 Azure Active Directory 驗證端點是在 [Microsoft 365 url 和 IP 位址範圍](/microsoft-365/enterprise/urls-and-ip-address-ranges) 檔的56和59章節中進行維護。 如需金鑰保存庫的指示，請參閱文件中的如何[在防火牆後存取 Azure 金鑰保存庫](../../key-vault/general/access-behind-firewall.md)。

### <a name="azure-instance-metadata-service"></a>Azure 執行個體中繼資料服務 

虛擬機器必須能夠存取 [Azure Instance Metadata service](instance-metadata-service.md) 端點，此端點會使用已知的非可路由 IP 位址， (只能 `169.254.169.254` 從 VM 記憶體取的) 。  不支援將本機 HTTP 流量更改為此位址的 Proxy 設定 (例如，新增 X-Forwarded-For 標頭)。

## <a name="next-steps"></a>後續步驟

- 查看更多有關[Azure 磁片加密疑難排解](disk-encryption-troubleshooting.md)的步驟
- [待用 Azure 資料加密](../../security/fundamentals/encryption-atrest.md)

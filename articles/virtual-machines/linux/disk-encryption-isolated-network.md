---
title: 在隔離的網路上 Azure 磁碟加密
description: 本文提供適用于 Linux Vm 的 Microsoft Azure 磁片加密的疑難排解秘訣。
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 02/27/2020
ms.custom: seodec18
ms.openlocfilehash: f0ca50d137ef7e6378d2bf8d0ab03127d49a9a88
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87373584"
---
# <a name="azure-disk-encryption-on-an-isolated-network"></a>在隔離的網路上 Azure 磁碟加密

當連線能力受防火牆、Proxy 需求或網路安全性群組 (NSG) 設定限制時，擴充功能執行所需工作的能力可能會受到中斷。 這個中斷會產生狀態訊息 (例如「無法在 VM 上使用擴充功能狀態」)。

## <a name="package-management"></a>套件管理

Azure 磁碟加密取決於許多元件，通常會在啟用 ADE 的過程中安裝（如果尚未存在的話）。 在防火牆後方或與網際網路隔離的情況下，必須在本機預先安裝或使用這些套件。

以下是每個散發所需的套件。 如需支援的散發版本和磁片區類型的完整清單，請參閱[支援的 vm 和作業系統](disk-encryption-overview.md#supported-vms-and-operating-systems)。

- **Ubuntu 14.04、16.04、18.04**： lsscsi、psmisc、at、cryptsetup-bin、python-parted、python-六、procps
- **CentOS 7.2-7.7**： lsscsi、psmisc、lvm2、uuid、at、patch、cryptsetup、cryptsetup-vm、pyparted、procps-ng、util-linux
- **CentOS 6.8**： lsscsi、psmisc、lvm2、uuid、at、cryptsetup-vm、pyparted、python-六
- **RedHat 7.2-7.7**： lsscsi、psmisc、lvm2、uuid、at、patch、cryptsetup、cryptsetup-vm、procps-ng、util-linux
- **RedHat 6.8**： lsscsi、psmisc、lvm2、uuid、at、patch、cryptsetup-vm
- **openSUSE 42.3、SLES 12-SP4、12-SP3**： lsscsi、cryptsetup

在 Red Hat 上，在需要 Proxy 時，您必須確保已正確地設定您的訂用帳戶管理員和 Yum。 如需詳細資訊，請參閱[如何針對訂用帳戶管理員和 Yum 問題進行疑難排解](https://access.redhat.com/solutions/189533)。  

手動安裝套件時，也必須在發行新版本時，以手動方式進行升級。

## <a name="network-security-groups"></a>網路安全性群組
任何套用的網路安全性群組設定仍然必須允許端點，從而符合磁碟加密的記載網路設定必要條件。  請參閱[Azure 磁碟加密：網路需求](disk-encryption-overview.md#networking-requirements)

## <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure AD 的 Azure 磁碟加密（舊版本）

如果搭配[Azure AD 使用 Azure 磁碟加密（舊版）](disk-encryption-overview-aad.md)，必須針對所有散發版本（除了散發版本適用的套件以外）手動安裝[Azure Active Directory 程式庫](../../active-directory/azuread-dev/active-directory-authentication-libraries.md)（如上所[列](#package-management)）。

使用 [Azure AD 認證](disk-encryption-linux-aad.md)啟用加密時，目標 VM 必須允許連線到 Azure Active Directory 端點和金鑰保存庫端點。 目前的 Azure Active Directory 驗證端點列在 [Office 365 URL 與 IP 位址範圍](/office365/enterprise/urls-and-ip-address-ranges)文件的第 56 和 59 節。 如需金鑰保存庫的指示，請參閱文件中的如何[在防火牆後存取 Azure 金鑰保存庫](../../key-vault/general/access-behind-firewall.md)。

### <a name="azure-instance-metadata-service"></a>Azure 執行個體中繼資料服務 

虛擬機器必須能夠存取[Azure 實例中繼資料服務](instance-metadata-service.md)端點，其使用的是已知且無法路由傳送的 IP 位址（）， `169.254.169.254` 且只能從 VM 記憶體取。  不支援將本機 HTTP 流量更改為此位址的 Proxy 設定 (例如，新增 X-Forwarded-For 標頭)。

## <a name="next-steps"></a>後續步驟

- 請參閱[Azure 磁片加密疑難排解](disk-encryption-troubleshooting.md)的其他步驟
- [待用 Azure 資料加密](../../security/fundamentals/encryption-atrest.md)

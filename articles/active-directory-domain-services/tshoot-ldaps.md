---
title: 在 Azure AD 域服務中排除安全 LDAP 故障 |微軟文檔
description: 瞭解如何為 Azure 活動目錄域服務託管域排除安全 LDAP （LDAPS） 的故障
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: 22d1b6e2344256b52cfdbc48720a680a770a4216
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132175"
---
# <a name="troubleshoot-secure-ldap-connectivity-issues-to-an-azure-active-directory-domain-services-managed-domain"></a>解決 Azure 活動目錄域服務託管域的安全 LDAP 連接問題

使用羽量級目錄訪問協定 （LDAP） 與 Azure 活動目錄域服務 （Azure AD DS） 通信的應用程式和服務可以[配置為使用安全的 LDAP](tutorial-configure-ldaps.md)。 必須打開適當的證書和所需的網路埠，才能安全 LDAP 正常工作。

本文可説明您解決 Azure AD DS 中安全 LDAP 訪問的問題。

## <a name="common-connection-issues"></a>常見連接問題

如果使用安全 LDAP 連接到 Azure AD DS 託管域時遇到問題，請查看以下故障排除步驟。 每次故障排除步驟後，請嘗試再次連接到 Azure AD DS 託管域：

* 安全 LDAP 憑證的簽發者鏈結在用戶端上必須受信任。 您可以將根憑證授權單位 （CA） 添加到用戶端上的受根信任憑證存放區，以建立信任。
    * 請確保[匯出證書並將證書應用於用戶端電腦][client-cert]。
* 驗證託管域的安全 LDAP 證書在 *"主題"* 或 *"主題替代名稱"* 屬性中具有 DNS 名稱。
    * 查看[安全的 LDAP 證書要求][certs-prereqs]，並在需要時創建替換證書。
* 驗證 LDAP 用戶端（如*ldp.exe）* 是否使用 DNS 名稱而不是 IP 位址連接到安全的 LDAP 終結點。
    * 應用於 Azure AD DS 託管域的證書不包括服務的 IP 位址，僅包括 DNS 名稱。
* 檢查 LDAP 用戶端連線到的 DNS 名稱。 它必須解析為 Azure AD DS 託管域上的安全 LDAP 的公共 IP 位址。
    * 如果 DNS 名稱解析為內部 IP 位址，請更新 DNS 記錄以解析為外部 IP 位址。
* 對於外部連接，網路安全性群組必須包含一個規則，允許流量從 Internet 到 TCP 埠 636。
    * 如果可以使用安全 LDAP 從直接連接到虛擬網路的資源而不是外部連接連接到 Azure AD DS 託管域，請確保[創建網路安全性群組規則以允許安全的 LDAP 流量][ldaps-nsg]。

## <a name="next-steps"></a>後續步驟

如果仍有問題，[請打開 Azure 支援請求][azure-support]以獲取其他故障排除説明。

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
[certs-prereqs]: tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap
[client-cert]: tutorial-configure-ldaps.md#export-a-certificate-for-client-computers
[ldaps-nsg]: tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet

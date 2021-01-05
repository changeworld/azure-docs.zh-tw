---
title: 在 Azure HPC Cache 中使用擴充群組
description: 如何在 Azure HPC Cache 中設定目錄服務以供用戶端存取儲存體目標
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 12/22/2020
ms.author: v-erkel
ms.openlocfilehash: 28265861c98cceaedf7d2662f6526a9f62fe68de
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803760"
---
# <a name="configure-directory-services"></a>設定目錄服務

**目錄服務** 設定可讓您的 Azure HPC Cache 使用外部來源來驗證使用者，以存取後端儲存體。

如果您的工作流程包含的 NFS 儲存體目標和用戶端是超過16個群組的成員，您可能需要啟用 **擴充群組** 。

按一下按鈕以啟用擴充群組之後，您必須選擇 Azure HPC Cache 將用來取得使用者和群組認證的來源。

* [Active Directory](#configure-active-directory) -從外部 Active Directory 伺服器取得認證。 您無法使用此工作的 Azure Active Directory。
* 一般[檔案-](#configure-file-download) `/etc/group` `/etc/passwd` 從網路位置下載及下載檔案。
* [Ldap](#configure-ldap) -取得輕量型目錄存取協定 (ldap) 相容來源的認證。

> [!NOTE]
> 請確定您的快取可以從其安全的子網中存取其群組資訊來源。<!-- + details/examples -->

[已下載的使用者 **名稱** ] 欄位會顯示最新群組資訊下載的狀態。

![入口網站中 [目錄服務] 頁面設定頁面的螢幕擷取畫面，其中已選取 [擴充群組] 的 [是] 選項，以及標示為 [下載來源開啟] 的下拉式功能表](media/directory-services-select-group-source.png)

## <a name="configure-active-directory"></a>設定 Active Directory

本節說明如何設定快取，以從外部 Active Directory (AD) 伺服器取得使用者和群組認證。

在 [ **Active directory 詳細資料**] 底下，提供下列值：

* **主要 DNS** -指定可供快取用來解析 AD 功能變數名稱之功能變數名稱伺服器的 IP 位址。

* **次要 DNS** (選擇性) -輸入當主伺服器無法使用時，要使用之名稱伺服器的位址。

* **AD DNS 功能變數名稱** -提供快取所要加入之 ad 伺服器的完整功能變數名稱，以取得認證。

* 快取 **伺服器名稱 (電腦帳戶)** -設定將在加入 AD 網域時指派給此 HPC Cache 的名稱。 請指定容易辨識的名稱作為此快取。 名稱長度最多可以有15個字元，且可包含大寫或小寫字母、數位和連字號， ( ) 。

在 [ **認證** ] 區段中，提供 Azure HPC Cache 可以用來存取 ad 伺服器的 ad 系統管理員使用者名稱和密碼。 這項資訊會在儲存時加密，無法進行查詢。

按一下頁面頂端的按鈕來儲存設定。

![[下載詳細資料] 區段的螢幕擷取畫面，其中已填入 Active Directory 值](media/group-download-details-ad.png)

## <a name="configure-file-download"></a>設定檔案下載

如果您想要使用使用者和群組資訊來下載檔案，則需要這些值。 這些檔案必須是標準的 Linux/UNIX `/etc/group` 和 `/etc/passwrd` 格式。

* **使用者檔案 uri** ：輸入檔案的完整 URI `/etc/passwrd` 。
* **群組檔案 URI** ：輸入檔案的完整 URI `/etc/group` 。

![一般檔案下載的 [下載詳細資料] 區段的螢幕擷取畫面](media/group-download-details-file.png)

## <a name="configure-ldap"></a>設定 LDAP

如果您想要使用非 AD LDAP 來源來取得使用者和群組認證，請填入這些值。 如果您需要這些值的協助，請洽詢您的 LDAP 系統管理員。

* **Ldap 伺服器** -輸入要使用之 ldap 伺服器的完整功能變數名稱或 IP 位址。 <!-- only one, not up to 3 -->

* **Ldap 基底 DN** -以 DN 格式指定 LDAP 網域的基本辨別名稱。 如果您不知道您的基底 DN，請詢問您的 LDAP 系統管理員。

伺服器和基底 DN 是讓 LDAP 正常運作的唯一必要設定，但其他選項可讓您的連接更安全。

![[目錄服務頁面設定] 頁面上 [LDAP 設定] 區域的螢幕擷取畫面](media/group-download-details-ldap.png)

在 [ **安全存取** ] 區段中，您可以啟用 LDAP 連接的加密和憑證驗證。 按一下 **[是]** 以啟用加密之後，您就可以使用下列選項：

* **需要有效的憑證** -當設定此項時，會針對下列 URI 欄位中的憑證授權單位單位驗證 LDAP 伺服器的憑證。

* **CA 憑證 URI** -指定授權憑證的路徑。 這可以是已驗證 CA 的憑證或自我簽署憑證的連結。 此欄位必須使用外部驗證的憑證設定。

* **自動下載憑證** -如果您想要在提交這些設定時立即嘗試下載憑證，請選擇 **[是]** 。

如果您想要使用 LDAP 安全性的靜態認證，請填寫 [ **認證** ] 區段。

* 系結 **DN** -輸入要用來向 LDAP 伺服器驗證的系結辨別名稱。  (使用 DN 格式。 ) 
* 系結 **密碼**-提供 bind DN 的密碼。

## <a name="next-steps"></a>後續步驟

* 深入瞭解[掛接 Azure HPC Cache](hpc-cache-mount.md)中的用戶端存取
* 如果您的認證未正確下載，請洽詢系統管理員以取得您的認證來源。 如有需要，請開啟 [支援票證](hpc-cache-support-ticket.md) 。

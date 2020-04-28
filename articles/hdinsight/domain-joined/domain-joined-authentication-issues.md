---
title: Azure HDInsight 中的驗證問題
description: Azure HDInsight 中的驗證問題
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.openlocfilehash: 26eec9cdd327ceb51e72deb1d6f40d585ce368fb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75896129"
---
# <a name="authentication-issues-in-azure-hdinsight"></a>Azure HDInsight 中的驗證問題

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方法。

在 Azure Data Lake （Gen1 或 Gen2）支援的安全叢集上，當網域使用者透過 HDI 閘道（例如登入 Apache Ambari 入口網站）登入叢集服務時，HDI 閘道會先嘗試從 Azure Active Directory （Azure AD）取得 OAuth 權杖，然後從 Azure AD DS 取得 Kerberos 票證。 在任一階段中，驗證可能會失敗。 本文旨在進行這些問題的調試。

當驗證失敗時，系統會提示您輸入認證。 如果您取消此對話方塊，將會列印錯誤訊息。 以下是一些常見的錯誤訊息：

## <a name="invalid_grant-or-unauthorized_client-50126"></a>invalid_grant 或 unauthorized_client，50126

### <a name="issue"></a>問題

同盟使用者的登入失敗，錯誤碼為50126（雲端使用者登入成功）。 錯誤訊息類似于：

```
Reason: Bad Request, Detailed Response: {"error":"invalid_grant","error_description":"AADSTS70002: Error validating credentials. AADSTS50126: Invalid username or password\r\nTrace ID: 09cc9b95-4354-46b7-91f1-efd92665ae00\r\n Correlation ID: 4209bedf-f195-4486-b486-95a15b70fbe4\r\nTimestamp: 2019-01-28 17:49:58Z","error_codes":[70002,50126], "timestamp":"2019-01-28 17:49:58Z","trace_id":"09cc9b95-4354-46b7-91f1-efd92665ae00","correlation_id":"4209bedf-f195-4486-b486-95a15b70fbe4"}
```

### <a name="cause"></a>原因

Azure AD 錯誤碼50126表示租使用者`AllowCloudPasswordValidation`尚未設定此原則。

### <a name="resolution"></a>解決方法

Azure AD 租使用者的公司系統管理員應該啟用 Azure AD，才能針對 ADFS 支援的使用者使用密碼雜湊。  套用， `AllowCloudPasswordValidationPolicy`如在[HDInsight 中使用企業安全性套件](../domain-joined/apache-domain-joined-architecture.md)一文所示。

---

## <a name="invalid_grant-or-unauthorized_client-50034"></a>invalid_grant 或 unauthorized_client，50034

### <a name="issue"></a>問題

登入失敗，錯誤碼50034。 錯誤訊息類似于：

```
{"error":"invalid_grant","error_description":"AADSTS50034: The user account Microsoft.AzureAD.Telemetry.Diagnostics.PII does not exist in the 0c349e3f-1ac3-4610-8599-9db831cbaf62 directory. To sign into this application, the account must be added to the directory.\r\nTrace ID: bbb819b2-4c6f-4745-854d-0b72006d6800\r\nCorrelation ID: b009c737-ee52-43b2-83fd-706061a72b41\r\nTimestamp: 2019-04-29 15:52:16Z", "error_codes":[50034],"timestamp":"2019-04-29 15:52:16Z","trace_id":"bbb819b2-4c6f-4745-854d-0b72006d6800", "correlation_id":"b009c737-ee52-43b2-83fd-706061a72b41"}
```

### <a name="cause"></a>原因

使用者名稱不正確（不存在）。 使用者未使用 Azure 入口網站中使用的相同使用者名稱。

### <a name="resolution"></a>解決方法

使用在該入口網站中運作的相同使用者名稱。

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant 或 unauthorized_client，50053

### <a name="issue"></a>問題

使用者帳戶已被鎖定，錯誤碼50053。 錯誤訊息類似于：

```
{"error":"unauthorized_client","error_description":"AADSTS50053: You've tried to sign in too many times with an incorrect user ID or password.\r\nTrace ID: 844ac5d8-8160-4dee-90ce-6d8c9443d400\r\nCorrelation ID: 23fe8867-0e8f-4e56-8764-0cdc7c61c325\r\nTimestamp: 2019-06-06 09:47:23Z","error_codes":[50053],"timestamp":"2019-06-06 09:47:23Z","trace_id":"844ac5d8-8160-4dee-90ce-6d8c9443d400","correlation_id":"23fe8867-0e8f-4e56-8764-0cdc7c61c325"}
```

### <a name="cause"></a>原因

使用不正確的密碼嘗試登入太多次。

### <a name="resolution"></a>解決方法

等候30分鐘，或停止任何可能嘗試進行驗證的應用程式。

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant 或 unauthorized_client，50053

### <a name="issue"></a>問題

密碼已過期，錯誤碼50053。 錯誤訊息類似于：

```
{"error":"user_password_expired","error_description":"AADSTS50055: Password is expired.\r\nTrace ID: 241a7a47-e59f-42d8-9263-fbb7c1d51e00\r\nCorrelation ID: c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a\r\nTimestamp: 2019-06-06 17:29:37Z","error_codes":[50055],"timestamp":"2019-06-06 17:29:37Z","trace_id":"241a7a47-e59f-42d8-9263-fbb7c1d51e00","correlation_id":"c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a","suberror":"user_password_expired","password_change_url":"https://portal.microsoftonline.com/ChangePassword.aspx"}
```

### <a name="cause"></a>原因

密碼已過期。

### <a name="resolution"></a>解決方法

變更 Azure 入口網站中的密碼（在您的內部部署系統上），然後等待30分鐘讓同步處理趕上。

---

## <a name="interaction_required"></a>interaction_required

### <a name="issue"></a>問題

接收錯誤訊息`interaction_required`。

### <a name="cause"></a>原因

使用者套用了條件式存取原則或 MFA。 由於尚不支援互動式驗證，因此使用者或叢集必須從 MFA/條件式存取中免除。 如果您選擇豁免叢集（以 IP 位址為基礎的豁免原則），請確定 AD `ServiceEndpoints`已針對該 vnet 啟用。

### <a name="resolution"></a>解決方法

使用條件式存取原則，並豁免 HDInisght 叢集的 MFA，如[使用 Azure Active Directory Domain Services 設定具有企業安全性套件的 HDInsight](./apache-domain-joined-configure-using-azure-adds.md)叢集中所示。

---

## <a name="sign-in-denied"></a>拒絕登入

### <a name="issue"></a>問題

已拒絕登入。

### <a name="cause"></a>原因

若要進入此階段，您的 OAuth 驗證不是問題，但 Kerberos 驗證是。 如果此叢集是由 ADLS 支援，則 OAuth 登入在嘗試 Kerberos 驗證之前已成功。 在 WASB 叢集上，不會嘗試 OAuth 登入。 Kerberos 失敗的原因有很多，例如密碼雜湊不同步、使用者帳戶在 Azure AD DS 中遭到鎖定等等。 只有在使用者變更密碼時，才會同步處理密碼雜湊。 當您建立 Azure AD DS 實例時，它會開始同步在建立後變更的密碼。 它不會追溯在開始之前設定的同步密碼。

### <a name="resolution"></a>解決方法

如果您認為密碼可能不會同步，請嘗試變更密碼，並等候幾分鐘的時間進行同步處理。

嘗試透過 SSH 連線到，您將需要嘗試使用相同的使用者認證，從已加入網域的電腦進行驗證（kinit）。 使用本機使用者透過 SSH 連線到前端/邊緣節點，然後執行 kinit。

---

## <a name="kinit-fails"></a>kinit 失敗

### <a name="issue"></a>問題

Kinit 失敗。

### <a name="cause"></a>原因

變動。

### <a name="resolution"></a>解決方法

若要讓 kinit 成功，您必須知道您`sAMAccountName`的（這是不含領域的簡短帳戶名稱）。 `sAMAccountName`通常是帳戶前置詞（例如中`bob@contoso.com`的 bob）。 某些使用者可能會有不同的。 您將需要流覽/搜尋目錄的功能，以瞭解您`sAMAccountName`的。

尋找`sAMAccountName`方法：

* 如果您可以使用本機 Ambari 系統管理員的身分登入 Ambari，請查看使用者清單。

* 如果您有已[加入網域的 windows 機器](../../active-directory-domain-services/manage-domain.md)，您可以使用標準的 windows AD 工具進行流覽。 這需要網域中的工作帳戶。

* 從前端節點，您可以使用 SAMBA 命令進行搜尋。 這需要有效的 Kerberos 會話（成功 kinit）。 net ads 搜尋 "（userPrincipalName = bob *）"

    搜尋/流覽結果應該會顯示`sAMAccountName`屬性。 此外，您也可以查看其他屬性， `pwdLastSet`例如`badPasswordTime`、 `userPrincipalName`等等，以查看這些屬性是否符合您的預期。

---

## <a name="kinit-fails-with-preauthentication-failure"></a>kinit 失敗，發生預先驗證失敗

### <a name="issue"></a>問題

Kinit 失敗並`Preauthentication`出現失敗。

### <a name="cause"></a>原因

使用者名稱或密碼不正確。

### <a name="resolution"></a>解決方法

檢查您的使用者名稱和密碼。 另請檢查上面所述的其他屬性。 若要啟用詳細資訊調試`export KRB5_TRACE=/tmp/krb.log` ，請在嘗試 kinit 之前，先從會話執行。

---

## <a name="job--hdfs-command-fails-due-to-tokennotfoundexception"></a>作業/HDFS 命令因 TokenNotFoundException 而失敗

### <a name="issue"></a>問題

作業/HDFS 命令因而失敗`TokenNotFoundException`。

### <a name="cause"></a>原因

找不到所需的 OAuth 存取權杖，因此作業/命令會成功。 ADLS/ABFS 驅動程式會先嘗試從認證服務取得 OAuth 存取權杖，然後再提出儲存體要求。 當您使用相同的使用者登入 Ambari 入口網站時，會註冊此權杖。

### <a name="resolution"></a>解決方法

請確定您已成功透過身分識別用來執行作業的使用者名稱登入 Ambari 入口網站。

---

## <a name="error-fetching-access-token"></a>提取存取權杖時發生錯誤

### <a name="issue"></a>問題

使用者收到錯誤訊息`Error fetching access token`。

### <a name="cause"></a>原因

當使用者嘗試使用 Acl 存取 ADLS Gen2，且 Kerberos 權杖已過期時，就會發生此錯誤。

### <a name="resolution"></a>解決方法

* 如 Azure Data Lake Storage Gen1，請清除瀏覽器快取，然後再次登入 Ambari。

* 針對 Azure Data Lake Storage Gen2，請`/usr/lib/hdinsight-common/scripts/RegisterKerbWithOauth.sh <upn>`針對使用者嘗試登入的使用者執行

---

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

* 連接[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帳戶，以改善客戶體驗。 將 Azure 社區連接到正確的資源：解答、支援和專家。

* 如果您需要更多協助，您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**]，或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權，而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。

---
title: Azure HDInsight 中的身份驗證問題
description: Azure HDInsight 中的身份驗證問題
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.openlocfilehash: 26eec9cdd327ceb51e72deb1d6f40d585ce368fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75896129"
---
# <a name="authentication-issues-in-azure-hdinsight"></a>Azure HDInsight 中的身份驗證問題

本文介紹了與 Azure HDInsight 群集交互時問題的故障排除步驟和可能的解決方案。

在 Azure 資料湖（Gen1 或 Gen2）支援的安全群集上，當域使用者通過 HDI 閘道登錄到叢集服務（如登錄到 Apache Ambari 門戶）時，HDI 閘道將首先嘗試從 Azure 活動目錄 （Azure AD） 獲取 OAuth 權杖，然後從 Azure AD DS 獲取 Kerberos 票證。 身份驗證在其中任一階段都可能失敗。 本文旨在調試其中一些問題。

當身份驗證失敗時，系統會提示您獲取憑據。 如果取消此對話方塊，將列印錯誤訊息。 下面是一些常見的錯誤訊息：

## <a name="invalid_grant-or-unauthorized_client-50126"></a>invalid_grant或unauthorized_client， 50126

### <a name="issue"></a>問題

對於錯誤代碼 50126 的聯合使用者，登錄失敗（雲使用者登錄成功）。 錯誤訊息類似于：

```
Reason: Bad Request, Detailed Response: {"error":"invalid_grant","error_description":"AADSTS70002: Error validating credentials. AADSTS50126: Invalid username or password\r\nTrace ID: 09cc9b95-4354-46b7-91f1-efd92665ae00\r\n Correlation ID: 4209bedf-f195-4486-b486-95a15b70fbe4\r\nTimestamp: 2019-01-28 17:49:58Z","error_codes":[70002,50126], "timestamp":"2019-01-28 17:49:58Z","trace_id":"09cc9b95-4354-46b7-91f1-efd92665ae00","correlation_id":"4209bedf-f195-4486-b486-95a15b70fbe4"}
```

### <a name="cause"></a>原因

Azure AD 錯誤代碼 50126 表示`AllowCloudPasswordValidation`策略尚未由租戶設置。

### <a name="resolution"></a>解決方案

Azure AD 租戶的公司管理員應使 Azure AD 能夠對 ADFS 支援的使用者使用密碼雜湊。  `AllowCloudPasswordValidationPolicy`應用文章"[在 HDInsight 中使用企業安全包"中](../domain-joined/apache-domain-joined-architecture.md)所示。

---

## <a name="invalid_grant-or-unauthorized_client-50034"></a>invalid_grant或unauthorized_client， 50034

### <a name="issue"></a>問題

使用錯誤代碼 50034 登錄失敗。 錯誤訊息類似于：

```
{"error":"invalid_grant","error_description":"AADSTS50034: The user account Microsoft.AzureAD.Telemetry.Diagnostics.PII does not exist in the 0c349e3f-1ac3-4610-8599-9db831cbaf62 directory. To sign into this application, the account must be added to the directory.\r\nTrace ID: bbb819b2-4c6f-4745-854d-0b72006d6800\r\nCorrelation ID: b009c737-ee52-43b2-83fd-706061a72b41\r\nTimestamp: 2019-04-29 15:52:16Z", "error_codes":[50034],"timestamp":"2019-04-29 15:52:16Z","trace_id":"bbb819b2-4c6f-4745-854d-0b72006d6800", "correlation_id":"b009c737-ee52-43b2-83fd-706061a72b41"}
```

### <a name="cause"></a>原因

使用者名不正確（不存在）。 使用者不使用 Azure 門戶中使用的相同使用者名。

### <a name="resolution"></a>解決方案

使用該門戶中有效的相同使用者名。

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant或unauthorized_client，50053

### <a name="issue"></a>問題

使用者帳戶被鎖定，錯誤代碼 50053。 錯誤訊息類似于：

```
{"error":"unauthorized_client","error_description":"AADSTS50053: You've tried to sign in too many times with an incorrect user ID or password.\r\nTrace ID: 844ac5d8-8160-4dee-90ce-6d8c9443d400\r\nCorrelation ID: 23fe8867-0e8f-4e56-8764-0cdc7c61c325\r\nTimestamp: 2019-06-06 09:47:23Z","error_codes":[50053],"timestamp":"2019-06-06 09:47:23Z","trace_id":"844ac5d8-8160-4dee-90ce-6d8c9443d400","correlation_id":"23fe8867-0e8f-4e56-8764-0cdc7c61c325"}
```

### <a name="cause"></a>原因

使用錯誤密碼的登錄嘗試次數過多。

### <a name="resolution"></a>解決方案

等待 30 分鐘左右，停止任何可能嘗試進行身份驗證的應用程式。

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant或unauthorized_client，50053

### <a name="issue"></a>問題

密碼已過期，錯誤代碼 50053。 錯誤訊息類似于：

```
{"error":"user_password_expired","error_description":"AADSTS50055: Password is expired.\r\nTrace ID: 241a7a47-e59f-42d8-9263-fbb7c1d51e00\r\nCorrelation ID: c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a\r\nTimestamp: 2019-06-06 17:29:37Z","error_codes":[50055],"timestamp":"2019-06-06 17:29:37Z","trace_id":"241a7a47-e59f-42d8-9263-fbb7c1d51e00","correlation_id":"c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a","suberror":"user_password_expired","password_change_url":"https://portal.microsoftonline.com/ChangePassword.aspx"}
```

### <a name="cause"></a>原因

密碼已過期。

### <a name="resolution"></a>解決方案

更改 Azure 門戶中的密碼（在本地系統上），然後等待 30 分鐘以同步趕上。

---

## <a name="interaction_required"></a>interaction_required

### <a name="issue"></a>問題

接收錯誤訊息`interaction_required`。

### <a name="cause"></a>原因

使用者套用了條件式存取原則或 MFA。 由於尚不支援互動式驗證，因此使用者或叢集必須從 MFA/條件式存取中免除。 如果選擇免除群集（基於 IP 位址的豁免策略），請確保為該 vnet`ServiceEndpoints`啟用 AD。

### <a name="resolution"></a>解決方案

使用條件訪問策略，並使用 Azure 活動目錄域服務 將 HDInisght 群集從 MFA 中免除，如[使用 Azure 活動目錄域服務使用企業安全包配置 HDInsight 群集](./apache-domain-joined-configure-using-azure-adds.md)所示。

---

## <a name="sign-in-denied"></a>拒絕登錄

### <a name="issue"></a>問題

拒絕登錄。

### <a name="cause"></a>原因

要進入此階段，OAuth 身份驗證不是問題，但 Kerberos 身份驗證是問題。 如果此群集由 ADLS 支援，則在嘗試 Kerberos 身份驗證之前，OAuth 登錄已成功。 在 WASB 群集上，不會嘗試 OAuth 登錄。 Kerberos 失敗的原因可能有很多 - 如密碼雜湊不同步，使用者帳戶鎖定在 Azure AD DS 中，等等。 密碼雜湊僅在使用者更改密碼時同步。 創建 Azure AD DS 實例時，它將開始同步創建後更改的密碼。 它不會追溯同步在它開始之前設置的密碼。

### <a name="resolution"></a>解決方案

如果您認為密碼可能不同步，請嘗試更改密碼並等待幾分鐘進行同步。

嘗試將 SSH 放入 A 中，您需要嘗試使用相同的使用者憑據（從加入到域的電腦）進行身份驗證（kinit）。 SSH 與本地使用者一起進入頭/邊緣節點，然後運行 kinit。

---

## <a name="kinit-fails"></a>基尼特失敗

### <a name="issue"></a>問題

基尼特失敗了

### <a name="cause"></a>原因

變動。

### <a name="resolution"></a>解決方案

要獲得成功，您需要瞭解您的`sAMAccountName`（這是沒有領域的簡短帳戶名稱）。 `sAMAccountName`通常是帳戶首碼（如 中的`bob@contoso.com`bob）。 對於某些使用者，可能有所不同。 您需要能夠流覽/搜索目錄來瞭解您的`sAMAccountName`。

查找`sAMAccountName`方法 ：

* 如果您可以使用本地 Ambari 管理員登錄到 Ambari，請查看使用者清單。

* 如果您有[一個域聯接的視窗電腦](../../active-directory-domain-services/manage-domain.md)，您可以使用標準的 Windows AD 工具進行流覽。 這需要域中的工作帳戶。

* 從頭節點，您可以使用 SAMBA 命令進行搜索。 這需要有效的 Kerberos 會話（成功的 Kinit）。 網路廣告搜索"（使用者主要名稱_bob_）"

    搜索/流覽結果應顯示內容`sAMAccountName`。 此外，還可以查看其他屬性，如`pwdLastSet`、`badPasswordTime`等`userPrincipalName`，以查看這些屬性是否與預期屬性匹配。

---

## <a name="kinit-fails-with-preauthentication-failure"></a>Kinit 失敗，導致預身份驗證失敗

### <a name="issue"></a>問題

基尼特失敗`Preauthentication`。

### <a name="cause"></a>原因

使用者名或密碼不正確。

### <a name="resolution"></a>解決方案

檢查您的使用者名和密碼。 還要檢查上述其他屬性。 要啟用詳細調試，在嘗試`export KRB5_TRACE=/tmp/krb.log`kinit 之前從會話中運行。

---

## <a name="job--hdfs-command-fails-due-to-tokennotfoundexception"></a>作業 /HDFS 命令由於權杖未找到Exception而失敗

### <a name="issue"></a>問題

作業 /HDFS 命令由於`TokenNotFoundException`失敗而失敗。

### <a name="cause"></a>原因

找不到作業/命令成功所需的 OAuth 訪問權杖。 ADLS / ABFS 驅動程式將嘗試在發出存儲請求之前從憑據服務中檢索 OAuth 訪問權杖。 當您使用相同的使用者登錄到 Ambari 門戶時，將註冊此權杖。

### <a name="resolution"></a>解決方案

確保您已成功登錄到 Ambari 門戶一次，通過其標識用於運行作業的使用者名。

---

## <a name="error-fetching-access-token"></a>獲取訪問權杖錯誤

### <a name="issue"></a>問題

使用者收到錯誤訊息`Error fetching access token`。

### <a name="cause"></a>原因

當使用者嘗試使用 ACL 訪問 ADL 並 Kerberos 權杖已過期時，此錯誤間歇性地發生。

### <a name="resolution"></a>解決方案

* 對於 Azure 資料湖存儲第 1 代，請清除瀏覽器緩存並再次登錄到 Ambari。

* 對於 Azure 資料湖存儲 Gen2，為使用者嘗試登錄的使用者運行`/usr/lib/hdinsight-common/scripts/RegisterKerbWithOauth.sh <upn>`

---

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 用於改善客戶體驗的官方 Microsoft Azure 帳戶連接。 將 Azure 社區連接到正確的資源：答案、支援和專家。

* 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。

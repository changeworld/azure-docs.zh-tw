---
title: 如何管理 OPC 保存庫憑證服務 - Azure |微軟文檔
description: 管理 OPC 保存庫根 CA 憑證和使用者許可權。
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 890a25ed2cf11d657cad930815d78dbf968cc9f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "71203662"
---
# <a name="manage-the-opc-vault-certificate-service"></a>管理 OPC 保存庫憑證服務

本文介紹了 Azure 中 OPC 保存庫證書管理服務的管理工作。 它包括有關如何續訂頒發者 CA 憑證、如何續訂憑證撤銷清單 （CRL） 以及如何授予和撤銷使用者存取權限的資訊。

## <a name="create-or-renew-the-root-ca-certificate"></a>創建或續訂根 CA 憑證

部署 OPC 保存庫後，必須創建根 CA 憑證。 如果沒有有效的頒發者 CA 憑證，則無法簽名或頒發應用程式證書。 請參閱[證書](howto-opc-vault-secure-ca.md#certificates)以管理具有合理、安全存留期的證書。 在頒發者 CA 憑證的一半存留期後續訂它。 續訂時，還要考慮新簽名的應用程式證書的配置存留期不應超過頒發者 CA 憑證的存留期。
> [!IMPORTANT]
> 創建或續訂頒發者 CA 憑證需要管理員角色。

1. 在 打開憑證服務`https://myResourceGroup-app.azurewebsites.net`，然後登錄。
2. 轉到**證書組**。
3. 列出了一個預設證書組。 選取 [編輯]****。
4. 在 **"編輯證書組詳細資訊"** 中，可以修改 CA 和應用程式證書的主題名稱和存留期。 在頒發第一個 CA 憑證之前，應僅設置主題和存留期一次。 操作期間的存留期更改可能會導致頒發的證書和 CRL 的存留期不一致。
5. 輸入有效的主題（例如， `CN=My CA Root, O=MyCompany, OU=MyDepartment`<br>
   > [!IMPORTANT]
   > 如果更改主題，則必須續訂頒發者證書，否則服務將無法對應用程式證書進行簽名。 配置的主題根據活動頒發者證書的主題進行檢查。 如果主題不匹配，證書簽名將被拒絕。
6. 選取 [儲存]****。
7. 如果此時遇到"禁止"錯誤，則使用者憑據沒有管理員修改或創建新根憑證的許可權。 預設情況下，部署服務的使用者具有管理員並與服務簽名的角色。 需要在 Azure 活動目錄 （Azure AD） 應用程式註冊中根據需要將其他使用者添加到核准者、編寫者或管理員角色。
8. 選擇**詳細資訊**。 這將顯示更新的資訊。
9. 選擇**續訂 CA 憑證**以頒發第一個頒發者 CA 憑證，或續訂頒發者證書。 然後選擇 **"確定**"。
10. 幾秒鐘後，您將看到**證書詳細資訊**。 要下載最新的 CA 憑證和 CRL 以分發到 OPC UA 應用程式，請選擇 **"頒發者**"或 **"Crl**"。

現在，OPC UA 證書管理服務已準備好為 OPC UA 應用程式頒發證書。

## <a name="renew-the-crl"></a>續訂 CRL

CRL 的更新是一種更新，應定期分發給應用程式。 支援 CRL 發佈點 X509 擴展的 OPC UA 設備可以直接從微服務終結點更新 CRL。 其他 OPC UA 設備可能需要手動更新，或者可以使用 GDS 伺服器推送擴展 （*） 更新信任清單與證書和 CRL。

在以下工作流中，已刪除狀態中的所有證書請求都將在 CRL 中撤銷，這些證書對應于為其發出的頒發證書的頒發者 CA 憑證。 CRL 的版本號增加 1。 <br>
> [!NOTE]
> 所有頒發的 CRL 在頒發者 CA 憑證到期之前都有效。 這是因為 OPC UA 規範不需要 CRL 的強制性確定性分佈模型。

> [!IMPORTANT]
> 續訂頒發者 CRL 需要管理員角色。

1. 在 打開憑證服務`https://myResourceGroup.azurewebsites.net`，然後登錄。
2. 轉到**證書組**頁面。
3. 選擇**詳細資訊**。 這應顯示當前證書和 CRL 資訊。
4. 選擇 **"更新 CRL 吊銷清單 （CRL）"，** 以便為 OPC 保存庫存儲中的所有活動頒發者憑證發行更新的 CRL。
5. 幾秒鐘後，您將看到**證書詳細資訊**。 要下載最新的 CA 憑證和 CRL 以分發到 OPC UA 應用程式，請選擇 **"頒發者**"或 **"Crl**"。

## <a name="manage-user-roles"></a>管理使用者角色

在 Azure AD 企業應用程式中管理 OPC Vault 微服務的使用者角色。 有關角色定義的詳細說明，請參閱[角色](howto-opc-vault-secure-ca.md#roles)。

預設情況下，租戶中的經過身份驗證的使用者可以以 Reader 身份登錄服務。 較高的特權角色需要在 Azure 門戶中手動管理，或者使用 PowerShell。

### <a name="add-user"></a>新增使用者

1. 開啟 Azure 入口網站。
2. 轉到**Azure 活動目錄** > **企業應用程式**。
3. 選擇 OPC Vault 微服務的註冊（預設情況下，您的`resourceGroupName-service`）。
4. 轉到**使用者和組**。
5. 選擇 **"添加使用者**"。
6. 選擇或邀請使用者分配到特定角色。
7. 選擇使用者的角色。
8. 選擇 **"分配**"。
9. 對於管理員或核准者角色中的使用者，請繼續添加 Azure 金鑰保存庫訪問策略。

### <a name="remove-user"></a>移除使用者

1. 開啟 Azure 入口網站。
2. 轉到**Azure 活動目錄** > **企業應用程式**。
3. 選擇 OPC Vault 微服務的註冊（預設情況下，您的`resourceGroupName-service`）。
4. 轉到**使用者和組**。
5. 選擇要刪除的角色的使用者，然後選擇 **"刪除**"。
6. 對於管理員或核准者角色中的已刪除使用者，還要從 Azure 金鑰保存庫策略中刪除這些使用者。

### <a name="add-user-access-policy-to-azure-key-vault"></a>將使用者訪問策略添加到 Azure 金鑰保存庫

核准者和管理員需要其他訪問策略。

預設情況下，服務標識訪問金鑰保存庫的許可權有限，以防止在沒有使用者類比的情況下進行提升的操作或更改。 對於機密和證書，基本服務許可權為"獲取"和"清單"。 對於機密，只有一個例外：服務可以在使用者接受金鑰後從金鑰存儲中刪除它。 所有其他操作都需要使用者類比許可權。

#### <a name="for-an-approver-role-the-following-permissions-must-be-added-to-key-vault"></a>對於核准者角色，必須將以下許可權添加到金鑰保存庫

1. 開啟 Azure 入口網站。
2. 轉到部署期間使用的 OPC 保存庫`resourceGroupName`。
3. 轉到金鑰保存庫`resourceGroupName-xxxxx`。
4. 轉到**訪問策略**。
5. 選取 [新增]****。
6. 跳過範本。 沒有符合要求的範本。
7. **選擇"選擇主體**"，然後選擇要添加的使用者，或邀請新使用者到租戶。
8. 選擇以下**金鑰許可權**：**獲取**、**列出**和**簽名**。
9. 選擇以下**機密許可權**：**獲取**、**清單**、**設置**和**刪除**。
10. 選擇以下**證書許可權**：**獲取**和**列出**。
11. 選擇 **"確定**"，然後選擇 **"保存**"。

#### <a name="for-an-administrator-role-the-following-permissions-must-be-added-to-key-vault"></a>對於管理員角色，必須將以下許可權添加到金鑰保存庫

1. 開啟 Azure 入口網站。
2. 轉到部署期間使用的 OPC 保存庫`resourceGroupName`。
3. 轉到金鑰保存庫`resourceGroupName-xxxxx`。
4. 轉到**訪問策略**。
5. 選取 [新增]****。
6. 跳過範本。 沒有符合要求的範本。
7. **選擇"選擇主體**"，然後選擇要添加的使用者，或邀請新使用者到租戶。
8. 選擇以下**金鑰許可權**：**獲取**、**列出**和**簽名**。
9. 選擇以下**機密許可權**：**獲取**、**清單**、**設置**和**刪除**。
10. 選擇以下**證書許可權**：**獲取**、**清單**、**更新**、**創建**和**導入**。
11. 選擇 **"確定**"，然後選擇 **"保存**"。

### <a name="remove-user-access-policy-from-azure-key-vault"></a>從 Azure 金鑰保存庫中刪除使用者訪問策略

1. 開啟 Azure 入口網站。
2. 轉到部署期間使用的 OPC 保存庫`resourceGroupName`。
3. 轉到金鑰保存庫`resourceGroupName-xxxxx`。
4. 轉到**訪問策略**。
5. 查找要刪除的使用者，然後選擇 **"刪除**"。

## <a name="next-steps"></a>後續步驟

現在，您已經瞭解如何管理 OPC Vault 證書和使用者，您可以：

> [!div class="nextstepaction"]
> [OPC 設備的安全通信](howto-opc-vault-secure.md)

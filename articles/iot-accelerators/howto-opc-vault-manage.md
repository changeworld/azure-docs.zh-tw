---
title: 如何管理 OPC 保存庫憑證服務-Azure |Microsoft Docs
description: 管理 OPC 保存庫根 CA 憑證和使用者權限。
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 890a25ed2cf11d657cad930815d78dbf968cc9f9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "71203662"
---
# <a name="manage-the-opc-vault-certificate-service"></a>管理 OPC 保存庫憑證服務

本文說明 Azure 中的 OPC 保存庫憑證管理服務的管理工作。 其中包含有關如何更新簽發者 CA 憑證、如何更新憑證撤銷清單（CRL），以及如何授與及撤銷使用者存取權的資訊。

## <a name="create-or-renew-the-root-ca-certificate"></a>建立或更新根 CA 憑證

部署 OPC 保存庫之後，您必須建立根 CA 憑證。 如果沒有有效的簽發者 CA 憑證，您就無法簽署或發行應用程式憑證。 請參閱[憑證](howto-opc-vault-secure-ca.md#certificates)，以合理且安全的存留期來管理您的憑證。 在其存留期一半後更新簽發者 CA 憑證。 續訂時，也請考慮已設定新簽署應用程式憑證的存留期不應超過簽發者 CA 憑證的存留期。
> [!IMPORTANT]
> 必須要有系統管理員角色，才能建立或更新簽發者 CA 憑證。

1. 在`https://myResourceGroup-app.azurewebsites.net`中開啟您的憑證服務，然後登入。
2. 前往 [**憑證群組**]。
3. 有一個預設的憑證群組列出。 選取 [編輯]  。
4. 在 [**編輯憑證群組詳細資料**] 中，您可以修改 CA 和應用程式憑證的主體名稱和存留期。 只有在發行第一個 CA 憑證之前，應該只設定一次主體和存留期。 作業期間的存留期變更可能會導致所發行憑證和 Crl 的存留時間不一致。
5. 輸入有效的主旨（例如， `CN=My CA Root, O=MyCompany, OU=MyDepartment`）。<br>
   > [!IMPORTANT]
   > 如果您變更主旨，則必須更新簽發者憑證，否則服務將無法簽署應用程式憑證。 設定的主旨會針對作用中簽發者憑證的主旨進行檢查。 如果主體不相符，憑證簽署會遭到拒絕。
6. 選取 [儲存]  。
7. 如果此時遇到「禁止」的錯誤，表示您的使用者認證沒有系統管理員許可權，無法修改或建立新的根憑證。 根據預設，部署服務的使用者具有服務的系統管理員和簽署角色。 其他使用者必須在 Azure Active Directory （Azure AD）應用程式註冊中，視需要新增至核准者、作者或系統管理員角色。
8. 選取 [**詳細資料**]。 這應該會顯示更新的資訊。
9. 選取 [**更新 CA 憑證**] 以發行第一個簽發者 CA 憑證，或更新簽發者憑證。 然後選取 [確定]  。
10. 幾秒鐘後，您會看到**憑證詳細資料**。 若要下載最新的 CA 憑證和 CRL 以散發至您的 OPC UA 應用程式，請選取 [**簽發者**或**crl**]。

現在，OPC UA 憑證管理服務已準備好發行 OPC UA 應用程式的憑證。

## <a name="renew-the-crl"></a>更新 CRL

CRL 的續訂是更新，應定期散發給應用程式。 支援 CRL 發佈點 X509 延伸模組的 OPC UA 裝置，可以直接從微服務端點更新 CRL。 其他 OPC UA 裝置可能需要手動更新，或可以使用 GDS 伺服器推送延伸模組（*）來更新信任清單與憑證和 Crl。

在下列工作流程中，已刪除狀態中的所有憑證要求都會在 Crl 中撤銷，其對應至頒發證書的簽發者 CA 憑證。 CRL 的版本號碼會遞增1。 <br>
> [!NOTE]
> 所有發行的 Crl 在簽發者 CA 憑證到期前都是有效的。 這是因為 OPC UA 規格不需要 CRL 的強制確定性散發模型。

> [!IMPORTANT]
> 必須要有系統管理員角色，才能更新簽發者 CRL。

1. 在`https://myResourceGroup.azurewebsites.net`中開啟您的憑證服務，然後登入。
2. 移至 [**憑證群組**] 頁面。
3. 選取 [**詳細資料**]。 這應該會顯示目前的憑證和 CRL 資訊。
4. 選取 [**更新 Crl 撤銷清單（CRL）** ]，為 OPC 保存庫儲存體中所有作用中的簽發者憑證發出更新的 crl。
5. 幾秒鐘後，您會看到**憑證詳細資料**。 若要下載最新的 CA 憑證和 CRL 以散發至您的 OPC UA 應用程式，請選取 [**簽發者**或**crl**]。

## <a name="manage-user-roles"></a>管理使用者角色

您可以在 Azure AD 企業應用程式中管理 OPC 保存庫微服務的使用者角色。 如需角色定義的詳細描述，請參閱[角色](howto-opc-vault-secure-ca.md#roles)。

根據預設，租使用者中已驗證的使用者可以將服務以讀取器的形式登入。 較高的特殊許可權角色需要在 Azure 入口網站中或使用 PowerShell 進行手動管理。

### <a name="add-user"></a>新增使用者

1. 開啟 Azure 入口網站。
2. 前往**Azure Active Directory** > **企業應用程式**。
3. 選擇 [OPC 保存庫] 微服務的註冊（根據預設， `resourceGroupName-service`您的）。
4. 移至 [**使用者和群組**]。
5. 選取 [**新增使用者**]。
6. 選取或邀請使用者指派給特定角色。
7. 選取使用者的角色。
8. 選取 [**指派**]。
9. 對於 [系統管理員] 或 [核准者] 角色的使用者，請繼續新增 Azure Key Vault 存取原則。

### <a name="remove-user"></a>移除使用者

1. 開啟 Azure 入口網站。
2. 前往**Azure Active Directory** > **企業應用程式**。
3. 選擇 [OPC 保存庫] 微服務的註冊（根據預設， `resourceGroupName-service`您的）。
4. 移至 [**使用者和群組**]。
5. 選取要移除角色的使用者，然後選取 [**移除**]。
6. 若為 [系統管理員] 或 [核准者] 角色中移除的使用者，請同時從 Azure Key Vault 原則中移除它們。

### <a name="add-user-access-policy-to-azure-key-vault"></a>將使用者存取原則新增至 Azure Key Vault

核准者和系統管理員需要其他存取原則。

根據預設，服務識別僅具有存取 Key Vault 的有限許可權，以避免在沒有使用者模擬的情況下進行較高的作業或變更。 基本服務許可權為 [取得] 和 [清單]，適用于秘密和憑證。 針對秘密，只有一個例外狀況：服務可以在使用者接受私密金鑰後，從密碼存放區中將其刪除。 所有其他作業都需要使用者模擬許可權。

#### <a name="for-an-approver-role-the-following-permissions-must-be-added-to-key-vault"></a>若為核准者角色，必須將下列許可權新增至 Key Vault

1. 開啟 Azure 入口網站。
2. 移至您的 OPC `resourceGroupName`保存庫，在部署期間使用。
3. 移至 [Key Vault `resourceGroupName-xxxxx`]。
4. 移至 [**存取原則**]。
5. 選取 [新增]****。
6. 略過範本。 沒有符合需求的範本。
7. 選擇 [**選取主體**]，然後選取要新增的使用者，或將新的使用者邀請至租使用者。
8. 選取下列**金鑰許可權**： [**取得**]、[**列出**] 和 [**簽署**]。
9. 選取下列**秘密許可權**： [**取得**]、[**列出**]、[**設定**] 和 [**刪除**]。
10. 選取下列**憑證許可權**： [**取得**] 和 [**列出**]。
11. 選取 **[確定]**，然後選取 [**儲存**]。

#### <a name="for-an-administrator-role-the-following-permissions-must-be-added-to-key-vault"></a>對於系統管理員角色，必須將下列許可權新增至 Key Vault

1. 開啟 Azure 入口網站。
2. 移至您的 OPC `resourceGroupName`保存庫，在部署期間使用。
3. 移至 [Key Vault `resourceGroupName-xxxxx`]。
4. 移至 [**存取原則**]。
5. 選取 [新增]****。
6. 略過範本。 沒有符合需求的範本。
7. 選擇 [**選取主體**]，然後選取要新增的使用者，或將新的使用者邀請至租使用者。
8. 選取下列**金鑰許可權**： [**取得**]、[**列出**] 和 [**簽署**]。
9. 選取下列**秘密許可權**： [**取得**]、[**列出**]、[**設定**] 和 [**刪除**]。
10. 選取下列**憑證許可權**： [**取得**]、[**列出**]、[**更新**]、[**建立**] 和 [匯**入**]。
11. 選取 **[確定]**，然後選取 [**儲存**]。

### <a name="remove-user-access-policy-from-azure-key-vault"></a>從 Azure Key Vault 移除使用者存取原則

1. 開啟 Azure 入口網站。
2. 移至您的 OPC `resourceGroupName`保存庫，在部署期間使用。
3. 移至 [Key Vault `resourceGroupName-xxxxx`]。
4. 移至 [**存取原則**]。
5. 尋找要移除的使用者，然後選取 [**刪除**]。

## <a name="next-steps"></a>後續步驟

既然您已瞭解如何管理 OPC 保存庫憑證和使用者，您可以：

> [!div class="nextstepaction"]
> [安全的 OPC 裝置通訊](howto-opc-vault-secure.md)

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
ms.openlocfilehash: 0829d4b3fca068ddb0db2df53dd635ab7ad80bed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91281910"
---
# <a name="manage-the-opc-vault-certificate-service"></a>管理 OPC 保存庫憑證服務

> [!IMPORTANT]
> 當我們更新本文時，請參閱 [Azure 產業 IoT](https://azure.github.io/Industrial-IoT/) 以取得最新的內容。

本文說明 Azure 中的 OPC 保存庫憑證管理服務的系統管理工作。 其中包含有關如何更新簽發者 CA 憑證、如何更新 (CRL) 的憑證撤銷清單，以及如何授與及撤銷使用者存取權的資訊。

## <a name="create-or-renew-the-root-ca-certificate"></a>建立或更新根 CA 憑證

部署 OPC 保存庫之後，您必須建立根 CA 憑證。 如果沒有有效的簽發者 CA 憑證，您就無法簽署或發行應用程式憑證。 請參閱 [憑證](howto-opc-vault-secure-ca.md#certificates) 以合理且安全的存留期來管理您的憑證。 在其存留期的一半之後，更新簽發者 CA 憑證。 更新時，也請考慮新簽署之應用程式憑證的設定存留期不應超過簽發者 CA 憑證的存留期。
> [!IMPORTANT]
> 需要系統管理員角色，才能建立或更新簽發者 CA 憑證。

1. 開啟您的憑證服務 `https://myResourceGroup-app.azurewebsites.net` ，然後登入。
2. 移至 [ **憑證群組**]。
3. 列出一個預設的憑證群組。 選取 [編輯]。
4. 在 [ **編輯憑證群組詳細資料**] 中，您可以修改 CA 和應用程式憑證的主體名稱和存留期。 在第一個 CA 憑證發出之前，主體和存留期只能設定一次。 作業期間的存留期變更可能會導致發行的憑證和 Crl 的存留期不一致。
5. 輸入有效的主體 (例如 `CN=My CA Root, O=MyCompany, OU=MyDepartment`) 。<br>
   > [!IMPORTANT]
   > 如果您變更主旨，則必須更新簽發者憑證，否則服務將無法簽署應用程式憑證。 設定的主體會根據作用中簽發者憑證的主體進行檢查。 如果主體不符，則會拒絕憑證簽署。
6. 選取 [儲存]****。
7. 如果您在此時遇到「禁止」錯誤，則您的使用者認證沒有管理員許可權可修改或建立新的根憑證。 根據預設，部署服務的使用者會有服務的系統管理員和簽署角色。 其他使用者必須在 Azure Active Directory (Azure AD) 應用程式註冊中，適當地新增至核准者、寫入者或系統管理員角色。
8. 選取 [詳細資料]。 這應該會顯示更新的資訊。
9. 選取 [ **更新 CA 憑證** ] 以發出第一個簽發者 CA 憑證，或更新簽發者憑證。 然後選取 [確定]。
10. 幾秒鐘之後，您會看到 **憑證詳細資料**。 若要下載最新的 CA 憑證和 CRL 以散發至 OPC UA 應用程式，請選取 [ **簽發者** 或 **crl**]。

現在 OPC UA 憑證管理服務已準備好發行 OPC UA 應用程式的憑證。

## <a name="renew-the-crl"></a>更新 CRL

CRL 的更新是一項更新，應定期散發給應用程式。 支援 CRL 發佈點 X509 擴充功能的 OPC UA 裝置，可以直接從微服務端點更新 CRL。 其他 OPC UA 裝置可能需要手動更新，或可以使用 GDS 伺服器推送延伸模組 ( * ) 來更新信任清單，以使用憑證和 Crl 來更新信任清單。

在下列工作流程中，已刪除狀態中的所有憑證要求都會在 Crl 中撤銷，而這些要求會對應至其發出的簽發者 CA 憑證。 CRL 的版本號碼會遞增1。 <br>
> [!NOTE]
> 所有發行的 Crl 在簽發者 CA 憑證到期之前都有效。 這是因為 OPC UA 規格不需要適用于 CRL 的強制、決定性散發模型。

> [!IMPORTANT]
> 需要系統管理員角色才能更新簽發者的 CRL。

1. 開啟您的憑證服務 `https://myResourceGroup.azurewebsites.net` ，然後登入。
2. 移至 [ **憑證群組** ] 頁面。
3. 選取 [詳細資料]。 這應該會顯示目前的憑證和 CRL 資訊。
4. 選取 [ **更新 Crl 撤銷清單 (crl]) ** ，針對 OPC 保存庫儲存體中的所有作用中簽發者憑證發出更新的 crl。
5. 幾秒鐘之後，您會看到 **憑證詳細資料**。 若要下載最新的 CA 憑證和 CRL 以散發至 OPC UA 應用程式，請選取 [ **簽發者** 或 **crl**]。

## <a name="manage-user-roles"></a>管理使用者角色

您可以在 Azure AD 企業應用程式中管理 OPC 保存庫微服務的使用者角色。 如需角色定義的詳細描述，請參閱 [角色](howto-opc-vault-secure-ca.md#roles)。

根據預設，租使用者中已驗證的使用者可以用讀取器的身分登入服務。 具有較高許可權的角色需要在 Azure 入口網站中手動管理，或使用 PowerShell。

### <a name="add-user"></a>新增使用者

1. 開啟 Azure 入口網站。
2. 移至**Azure Active Directory**  >  **企業應用程式**。
3. 依預設，選擇微服務 (的 OPC 保存庫註冊 `resourceGroupName-service`) 。
4. 移至 [ **使用者和群組**]。
5. 選取 [ **新增使用者**]。
6. 選取或邀請使用者指派給特定角色。
7. 選取使用者的角色。
8. 選取 [指派]****。
9. 對於系統管理員或核准者角色中的使用者，請繼續新增 Azure Key Vault 存取原則。

### <a name="remove-user"></a>移除使用者

1. 開啟 Azure 入口網站。
2. 移至**Azure Active Directory**  >  **企業應用程式**。
3. 依預設，選擇微服務 (的 OPC 保存庫註冊 `resourceGroupName-service`) 。
4. 移至 [ **使用者和群組**]。
5. 選取要移除角色的使用者，然後選取 [ **移除**]。
6. 針對系統管理員或核准者角色中移除的使用者，也請從 Azure Key Vault 原則中移除這些使用者。

### <a name="add-user-access-policy-to-azure-key-vault"></a>將使用者存取原則新增至 Azure Key Vault

核准者和系統管理員需要額外的存取原則。

根據預設，服務身分識別僅具有存取 Key Vault 的有限許可權，以防止較高的作業或未進行使用者模擬的變更發生。 基本服務許可權是取得和列出秘密和憑證。 針對秘密，只有一個例外狀況：服務可以在使用者接受秘密存放區之後，從秘密存放區中刪除該私密金鑰。 所有其他作業都需要使用者模擬許可權。

#### <a name="for-an-approver-role-the-following-permissions-must-be-added-to-key-vault"></a>若為核准者角色，必須將下列許可權新增至 Key Vault

1. 開啟 Azure 入口網站。
2. 移至您的 OPC 保存庫 `resourceGroupName` ，在部署期間使用。
3. 移至 Key Vault `resourceGroupName-xxxxx` 。
4. 移至 [ **存取原則**]。
5. 選取 [新增]****。
6. 略過範本。 沒有符合需求的範本。
7. 選擇 [ **選取主體**]，然後選取要新增的使用者，或邀請新使用者加入租使用者。
8. 選取下列 **金鑰許可權**： **取得**、 **列出**和 **簽署**。
9. 選取下列 **秘密許可權**： **Get**、 **List**、 **Set**和 **Delete**。
10. 選取下列 **憑證許可權**： **Get** 和 **List**。
11. 選取 **[確定]**，然後選取 [ **儲存**]。

#### <a name="for-an-administrator-role-the-following-permissions-must-be-added-to-key-vault"></a>針對系統管理員角色，必須將下列許可權新增至 Key Vault

1. 開啟 Azure 入口網站。
2. 移至您的 OPC 保存庫 `resourceGroupName` ，在部署期間使用。
3. 移至 Key Vault `resourceGroupName-xxxxx` 。
4. 移至 [ **存取原則**]。
5. 選取 [新增]****。
6. 略過範本。 沒有符合需求的範本。
7. 選擇 [ **選取主體**]，然後選取要新增的使用者，或邀請新使用者加入租使用者。
8. 選取下列 **金鑰許可權**： **取得**、 **列出**和 **簽署**。
9. 選取下列 **秘密許可權**： **Get**、 **List**、 **Set**和 **Delete**。
10. 選取下列 **憑證許可權**： **取得**、 **列出**、 **更新**、 **建立**和匯 **入**。
11. 選取 **[確定]**，然後選取 [ **儲存**]。

### <a name="remove-user-access-policy-from-azure-key-vault"></a>從 Azure Key Vault 移除使用者存取原則

1. 開啟 Azure 入口網站。
2. 移至您的 OPC 保存庫 `resourceGroupName` ，在部署期間使用。
3. 移至 Key Vault `resourceGroupName-xxxxx` 。
4. 移至 [ **存取原則**]。
5. 尋找要移除的使用者，然後選取 [ **刪除**]。

## <a name="next-steps"></a>後續步驟

現在您已瞭解如何管理 OPC 保存庫憑證和使用者，您可以：

> [!div class="nextstepaction"]
> [安全的 OPC 裝置通訊](howto-opc-vault-secure.md)

---
title: 使用 Azure 入口網站來設定客戶管理的金鑰
titleSuffix: Cognitive Services
description: 瞭解如何使用 Azure 入口網站，以 Azure Key Vault 設定客戶管理的金鑰。 客戶管理的金鑰可讓您建立、輪替、停用及撤銷存取控制。
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 04a8a8d2520376931f2bb7727c1e751b83f6315f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "81455252"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>使用 Azure 入口網站以 Azure Key Vault 設定客戶管理的金鑰

您必須使用 Azure Key Vault 來儲存客戶管理的金鑰。 您可以建立自己的金鑰，並將其儲存在金鑰保存庫中，或者您可以使用 Azure Key Vault Api 來產生金鑰。 認知服務資源和金鑰保存庫必須位於相同的區域和相同的 Azure Active Directory （Azure AD）租使用者中，但它們可以在不同的訂用帳戶中。 如需 Azure Key Vault 的詳細資訊，請參閱[什麼是 Azure Key Vault？](https://docs.microsoft.com/azure/key-vault/key-vault-overview)。

本文說明如何使用[Azure 入口網站](https://portal.azure.com/)，以客戶管理的金鑰設定 Azure Key Vault。 若要瞭解如何使用 Azure 入口網站建立金鑰保存庫，請參閱[快速入門：使用 Azure 入口網站從 Azure Key Vault 設定和取出秘密](../../key-vault/secrets/quick-create-portal.md)。

## <a name="configure-azure-key-vault"></a>設定 Azure Key Vault

使用客戶管理的金鑰需要在金鑰保存庫上設定兩個屬性： [虛**刪除**] 和 [不要**清除**]。 這些屬性預設不會啟用，但可以使用 PowerShell 或 Azure CLI 在新的或現有的金鑰保存庫上啟用。

> [!IMPORTANT]
> 如果您沒有虛**刪除**，而且未啟用 [**清除**] 屬性，而您刪除了金鑰，您將無法復原認知服務資源中的資料。

若要瞭解如何在現有的金鑰保存庫上啟用這些屬性，請參閱下列其中一篇文章中標題為**啟用虛刪除**和**啟用清除保護**的章節：

- [如何使用 PowerShell](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell)進行虛刪除。
- [如何使用 CLI](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli)進行虛刪除。

Azure 儲存體加密僅支援大小為2048的 RSA 金鑰。 如需金鑰的詳細資訊，請參閱[關於 Azure Key Vault 金鑰、秘密和憑證](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)中的**Key Vault 金鑰**。

## <a name="enable-customer-managed-keys"></a>啟用客戶管理的金鑰

若要在 Azure 入口網站中啟用客戶管理的金鑰，請遵循下列步驟：

1. 流覽至您的認知服務資源。
1. 在您認知服務資源的 [**設定**] 分頁上，按一下 [**加密**]。 選取 [**客戶管理的金鑰**] 選項，如下圖所示。

    ![顯示如何選取客戶管理的金鑰的螢幕擷取畫面](../media/cognitive-services-encryption/selectcmk.png)

## <a name="specify-a-key"></a>指定金鑰

啟用客戶管理的金鑰後，您將有機會指定與認知服務資源相關聯的金鑰。

### <a name="specify-a-key-as-a-uri"></a>以 URI 形式指定金鑰

若要指定金鑰做為 URI，請遵循下列步驟：

1. 若要找出 Azure 入口網站中的金鑰 URI，請流覽至您的金鑰保存庫，然後選取 [**金鑰**] 設定。 選取所需的金鑰，然後按一下金鑰以查看其版本。 選取金鑰版本以查看該版本的設定。
1. 複製提供 URI 的 [**金鑰識別碼**] 欄位的值。

    ![顯示金鑰保存庫金鑰 URI 的螢幕擷取畫面](../media/cognitive-services-encryption/key-uri-portal.png)

1. 在儲存體帳戶的 [**加密**設定] 中，選擇 [**輸入金鑰 URI** ] 選項。
1. 將您複製的 URI 貼到 [**金鑰 URI** ] 欄位中。

   ![顯示如何輸入金鑰 URI 的螢幕擷取畫面](../media/cognitive-services-encryption/ssecmk2.png)

1. 指定包含金鑰保存庫的訂用帳戶。
1. 儲存您的變更。

### <a name="specify-a-key-from-a-key-vault"></a>從金鑰保存庫指定金鑰

若要從金鑰保存庫指定金鑰，請先確定您有包含金鑰的金鑰保存庫。 若要從金鑰保存庫指定金鑰，請遵循下列步驟：

1. 選擇 [從 Key Vault 選取]**** 選項。
1. 選取包含您要使用之金鑰的金鑰保存庫。
1. 選取金鑰保存庫中的金鑰。

   ![顯示客戶管理的金鑰選項的螢幕擷取畫面](../media/cognitive-services-encryption/ssecmk3.png)

1. 儲存您的變更。

## <a name="update-the-key-version"></a>更新金鑰版本

當您建立新版本的金鑰時，請更新認知服務資源，以使用新的版本。 請遵循下列步驟：

1. 流覽至您的認知服務資源，並顯示**加密**設定。
1. 輸入新金鑰版本的 URI。 或者，您可以再次選取金鑰保存庫和金鑰，以更新版本。
1. 儲存您的變更。

## <a name="use-a-different-key"></a>使用不同的金鑰

若要變更用於加密的金鑰，請遵循下列步驟：

1. 流覽至您的認知服務資源，並顯示**加密**設定。
1. 輸入新金鑰的 URI。 或者，您可以選取金鑰保存庫，然後選擇新的金鑰。
1. 儲存您的變更。

## <a name="disable-customer-managed-keys"></a>停用客戶管理的金鑰

當您停用客戶管理的金鑰時，您的認知服務資源就會使用 Microsoft 管理的金鑰進行加密。 若要停用客戶管理的金鑰，請遵循下列步驟：

1. 流覽至您的認知服務資源，並顯示**加密**設定。
1. 取消選取 [**使用您自己的金鑰**] 設定旁的核取方塊。

## <a name="next-steps"></a>後續步驟

* [什麼是 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)？
* [認知服務客戶管理的金鑰要求表單](https://aka.ms/cogsvc-cmk)
* [待用資料的臉部服務加密](../Face/face-encryption-of-data-at-rest.md)
* [待用資料的加密 QnA Maker](../QnAMaker/qna-maker-encryption-of-data-at-rest.md)
* [待用資料的 Language Understanding 服務加密](../LUIS/luis-encryption-of-data-at-rest.md)
* [待用資料的內容仲裁加密](../Content-Moderator/content-moderator-encryption-of-data-at-rest.md)
* [待用資料的 Translator 加密](../translator/translator-encryption-of-data-at-rest.md)
* [待用資料的個人化工具加密](../personalizer/personalizer-encryption-of-data-at-rest.md)

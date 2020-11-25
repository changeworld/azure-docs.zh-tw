---
title: 轉移 Azure 訂用帳戶的帳單擁有權
description: 說明如何將 Azure 訂用帳戶的帳單擁有權轉移給另一個帳戶。
keywords: 轉移 azure 訂用帳戶,azure 轉移訂用帳戶,將 azure 訂用帳戶移到另一個帳戶,azure 變更訂用帳戶擁有者,將 azure 訂用帳戶轉移給另一個帳戶, azure 轉移計費
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/11/2020
ms.author: banders
ms.custom: contperfq1
ms.openlocfilehash: ab71a7fa3d3f92ecf3e1f73835e41fa7170130e6
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2020
ms.locfileid: "94636600"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>將 Azure 訂用帳戶的帳單擁有權轉移給另一個帳戶

本文說明將 Azure 訂用帳戶的帳單擁有權轉移給另一個帳戶的必要步驟。 在您轉移訂用帳戶帳單擁有權之前，請參閱[關於轉移 Azure 訂用帳戶的帳單擁有權](../understand/subscription-transfer.md)。

如果您想要保留帳單擁有權，但變更訂用帳戶類型，請參閱[將 Azure 訂用帳戶切換為其他供應項目](switch-azure-offer.md)。 若要控制誰可以存取訂用帳戶中的資源，請參閱 [Azure 內建角色](../../role-based-access-control/built-in-roles.md)。

如果您是 Enterprise 合約 (EA) 客戶，您的企業系統管理員可以在帳戶之間轉移訂用帳戶的帳單擁有權。 如需詳細資訊，請[變更 Azure 訂用帳戶或帳戶擁有權](ea-portal-administration.md#change-azure-subscription-or-account-ownership)。

只有帳戶的計費管理員可以轉移訂用帳戶的擁有權。

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>轉移 Azure 訂用帳戶的帳單擁有權

1. 以計費帳戶 (具有您要轉移的訂用帳戶) 的管理員身分登入 [Azure 入口網站](https://portal.azure.com)。 如果您不確定您是否為系統管理員，或如果您需要判斷誰是系統管理員，請參閱[判斷帳戶計費管理員](../understand/subscription-transfer.md#whoisaa)。
1. 搜尋 [成本管理 + 帳單]  。  
   ![顯示 Azure 入口網站搜尋的螢幕擷取畫面](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)
1. 從左側窗格中選取 [訂用帳戶]。 視存取權而定，您可能需要選取計費範圍，然後選取 [訂用帳戶] 或 [Azure 訂用帳戶]。
1. 針對您要轉移的訂用帳戶選取 [轉移帳單擁有權]。  
   ![選取要轉移的訂用帳戶](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)
1. 輸入使用者的電子郵件地址，該使用者是將成為訂用帳戶新擁有者之帳戶的計費管理員。
1. 如果您要將訂用帳戶轉移至另一個 Azure AD 租用戶中的帳戶，請選取是否要將訂用帳戶移至新帳戶的租用戶。 如需詳細資訊，請參閱[將訂用帳戶轉移給另一個 Azure AD 租用戶中的帳戶](#transfer-a-subscription-to-another-azure-ad-tenant-account)。
    > [!IMPORTANT]
    > 如果您選擇將訂用帳戶移至新帳戶的 Azure AD 租用戶，則會永久移除訂用帳戶中用於存取資源的所有 [Azure 角色指派](../../role-based-access-control/role-assignments-portal.md)。 只有新帳戶中接受轉移要求的使用者，才有權利管理訂用帳戶中的資源。 或者，您可以清除 [訂用帳戶 Azure AD 租用戶] 選項以轉移帳單擁有權，而不需將訂用帳戶移至新帳戶的租用戶。 如果這麼做，現有的 Azure 角色指派仍可存取 Azure 資源。  
    ![傳送轉移頁面](./media/billing-subscription-transfer/billing-send-transfer-request.png)
1. 選取 [傳送轉移要求]。
1. 使用者會收到一封電子郵件，內含用來檢閱轉移要求的指示。  
   ![傳送給接受者的訂用帳戶移轉電子郵件](./media/billing-subscription-transfer/billing-receiver-email.png)
1. 若要核准轉移要求，使用者須選取電子郵件中的連結，並遵循相關指示。 接著，使用者會選取將用來支付訂用帳戶的付款方式。 如果使用者沒有 Azure 帳戶，他們就必須註冊新帳戶。  
   ![第一個訂用帳戶轉移網頁](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)
   ![第二個訂用帳戶轉移網頁](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)
   ![第三個訂用帳戶轉移網頁](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)
1. 成功！ 現在已移轉訂用帳戶。

## <a name="transfer-a-subscription-to-another-azure-ad-tenant-account"></a>將訂用帳戶轉移給另一個 Azure AD 租用戶帳戶

當您註冊 Azure 時，系統會為您建立 Azure Active Directory (AD) 租用戶。 此租用戶代表您的帳戶。 您可以使用此租用戶來管理訂用帳戶和資源的存取權。

當您建立新的訂用帳戶時，該帳戶會裝載於您帳戶的 Azure AD 租用戶中。 如果您想要將訂用帳戶或其資源的存取權提供給其他人，則必須邀請他們加入您的租用戶。 這麼做可協助您控制訂用帳戶和資源的存取權。

當您將訂用帳戶的帳單擁有權轉移給另一個 Azure AD 租用戶中的帳戶時，您可以將訂用帳戶移至新帳戶的租用戶。 如果您這麼做，所有以 [Azure 角色指派](../../role-based-access-control/role-assignments-portal.md)來管理訂用帳戶及其資源的使用者、群組或服務主體都會失去其存取權。 只有新帳戶中接受轉移要求的使用者，才有權利管理資源。 新的擁有者必須以手動方式將這些使用者新增至訂用帳戶，以將存取權提供給失去存取權的人員。 如需詳細資訊，請參閱[將 Azure 訂用帳戶轉移至不同的 Azure AD 目錄](../../role-based-access-control/transfer-subscription.md)。

## <a name="transfer-visual-studio-and-partner-network-subscriptions"></a>轉移 Visual Studio 和合作夥伴網路訂用帳戶

Visual Studio 和 Microsoft 合作夥伴網路訂用帳戶都有相關聯的每月週期性 Azure 點數。 當您轉移這些訂用帳戶時，點數無法在目的地計費帳戶中使用。 訂用帳戶會在目的地計費帳戶中使用此點數。 例如，如果 Bob 在 9 月 9 日將 Visual Studio Enterprise 訂用帳戶轉移給 Jane 的帳戶，且 Jane 接受轉移。 完成轉移之後，訂用帳戶會開始在 Jane 的帳戶中使用點數。 點數將會在每個月第九天重設。

## <a name="next-steps-after-accepting-billing-ownership"></a>接受帳單擁有權後的後續步驟

如果您已接受 Azure 訂用帳戶的帳單擁有權，建議您檢閱下列後續步驟：

1. 請檢閱並更新服務管理員、共同管理員和 Azure 角色指派。 若要深入了解，請參閱[新增或變更 Azure 訂用帳戶管理員](add-change-subscription-administrator.md)及[使用 Azure 入口網站新增或移除 Azure 角色指派](../../role-based-access-control/role-assignments-portal.md)。
1. 更新與此訂用帳戶服務相關聯的認證，包括：
   1. 可將使用者管理權限授與給訂用帳戶資源的管理憑證。 如需詳細資訊，請參閱 [建立和上傳 Azure 的管理憑證](../../cloud-services/cloud-services-certs-create.md)
   1. 服務 (例如儲存體) 的存取金鑰。 如需詳細資訊，請參閱[關於 Azure 儲存體帳戶](../../storage/common/storage-account-create.md)
   1. 服務 (例如 Azure 虛擬機器) 的遠端存取認證。
1. 如果您正與合作夥伴協力作業，請考慮更新訂用帳戶的合作夥伴 ID。 您可以在 [Azure 入口網站](https://portal.azure.com)更新合作夥伴識別碼。 如需詳細資訊，請參閱[將合作夥伴識別碼連結到您的 Azure 帳戶](link-partner-id.md)。

## <a name="troubleshooting"></a>疑難排解

如果您在轉移訂用帳戶時遇到問題，請使用下列疑難排解資訊。

### <a name="the-transfer-subscription-option-is-unavailable"></a>[轉移訂用帳戶] 選項無法使用

<a name="no-button"></a> 

您的計費帳戶無法使用自助式訂用帳戶轉移。 目前，我們不支援在 Azure 入口網站中，轉移 Enterprise 合約 (EA) 帳戶中的訂用帳戶帳單擁有權。 此外，與 Microsoft 代表共同建立的 Microsoft 客戶合約帳戶不支援轉移帳單擁有權。

###  <a name="not-all-subscription-types-can-transfer"></a>並非所有訂用帳戶類型都可以轉移

並非所有的訂用帳戶類型都支援帳單擁有權轉移。 若要檢視支援轉移的訂用帳戶類型清單，請參閱[支援的訂用帳戶類型](../understand/subscription-transfer.md#supported-subscription-types)

###  <a name="access-denied-error-shown-when-trying-to-transfer-subscription-billing-ownership"></a>嘗試轉移訂用帳戶帳單擁有權時顯示拒絕存取錯誤

如果您嘗試轉移 Microsoft Azure 方案訂用帳戶，但沒有必要的權限，您就會看到此錯誤。 若要轉移 Microsoft Azure 方案訂用帳戶，您在訂用帳戶計費的發票區段上必須是擁有者或參與者。 如需詳細資訊，請參閱[管理發票區段的訂用帳戶](../manage/understand-mca-roles.md#manage-subscriptions-for-invoice-section)。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

- 請檢閱並更新服務管理員、共同管理員和 Azure 角色指派。 若要深入了解，請參閱[新增或變更 Azure 訂用帳戶管理員](add-change-subscription-administrator.md)及[使用 Azure 入口網站新增或移除 Azure 角色指派](../../role-based-access-control/role-assignments-portal.md)。
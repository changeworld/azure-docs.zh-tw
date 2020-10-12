---
title: 設定 Azure 資料箱的加密箱
description: 瞭解如何搭配使用客戶加密箱與 Azure 資料箱。
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 07/10/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: 9c95760d03db976b59537adcecbe39a942b72126
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86209372"
---
# <a name="use-customer-lockbox-for-azure-data-box-preview"></a>使用 Azure 資料箱 (Preview 的客戶加密箱) 

Azure 資料箱是用來在 Azure 之間傳輸客戶資料。 在某些情況下，Microsoft 支援服務可能需要在支援要求期間存取客戶資料。 您可以使用客戶加密箱作為介面，來檢查和核准或拒絕這些資料存取要求。 

本文涵蓋如何針對資料箱匯入和匯出訂單來起始和追蹤客戶加密箱要求。 本文同時適用于 Azure 資料箱裝置和 Azure Data Box Heavy 裝置。 

## <a name="devops-workflow-for-data-access"></a>DevOps 資料存取的工作流程

Microsoft 的支援和資料箱作業小組通常不會存取客戶資料。 他們會嘗試使用標準工具和遙測來解決問題。 <!--The only scenarios where there is a need to access customer data is when there is an issue with the data that needs to be fixed. For example, if the data is copied to a wrong folder or is in an incorrect format and is likely to result in an upload or download failure, then Microsoft will try to access your data in the Azure datacenter.--> 

如果無法解決問題，而且需要 Microsoft 支援服務來調查或修復資料，則會透過及時 (JIT) 入口網站來要求更高的存取權。 JIP 入口網站會驗證許可權等級、提供多重要素驗證，也包含來自內部 Microsoft 核准者的核准。 例如，核准者可能是 DevOps Manager。 

在您透過 JIT 入口網站核准提升存取權的要求之後，如果您已啟用加密箱，Microsoft 也會要求您明確同意存取資料。 您可以透過入口網站中的客戶加密箱服務來要求和追蹤存取權。 

如果您尚未啟用加密箱，則不需要您的同意就能存取資料。


## <a name="prerequisites-for-access-request"></a>存取要求的必要條件

在您開始前，請確定：

1. 您已根據中的指示建立了 Azure 資料箱訂單：
    1. 教學課程：匯入訂單的[Azure 資料箱順序](data-box-deploy-ordered.md)。
    1. [教學課程：排序](data-box-deploy-export-ordered.md) 匯出訂單的 Azure 資料箱。

2. 您已設定資料箱的客戶加密箱。 這是可加入宣告的服務。 

    1. 適用于資料箱服務的客戶加密箱目前為預覽狀態。 若要為您的組織啟用資料箱的客戶加密箱，請註冊 [Azure 公開預覽客戶加密箱](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Kwz02N6XVCoKNpxIpqE_hUNzlTUUNYVkozOVlFNVRSWDVHRkkwTFQyViQlQCN0PWcu)。
    2. 如果所有客戶的 Azure 支援方案都具有基本等級的開發人員，客戶加密箱會自動提供。 <!--How do you enable Lockbox? change this for Azure Data Box, perhaps you need a different support plan When you have an eligible support plan, no action is required by you to enable Customer Lockbox. Customer Lockbox requests are initiated by a Microsoft engineer if this action is needed to progress a support ticket that is filed from somebody in your organization.-->

3. 已針對此問題開啟服務要求或支援票證。 如需支援票證的詳細資訊，請參閱為 [資料箱提出服務要求](data-box-disk-contact-microsoft-support.md)。


## <a name="track-approve-request-via-lockbox"></a>追蹤，透過加密箱核准要求

若要追蹤並核准存取客戶資料的要求，請遵循下列步驟：

1. Microsoft 偵測到在 Azure 資料中心上傳或下載資料時發生問題。 例如，資料箱訂單會在 **資料複製** 階段暫停。 

    支援工程師會透過支援會話連線到資料箱，並嘗試使用標準工具和遙測進行問題的疑難排解。 如果資料箱磁片已鎖定，且共用無法存取，則支援工程師會建立加密箱要求。 
 
2. 建立要求時，通常會將通知傳送給訂用帳戶管理員，但您也可以設定通知群組。 

3. 您可以在 Azure 入口網站中看到加密箱要求，以供您核准。 

    ![Azure 入口網站中的要求](./media/data-box-customer-lockbox/3-lockbox-request-azure-portal.png)

    若要從入口網站核准加密箱要求，您可以選取 [ **核准**]。

    ![核准要求](./media/data-box-customer-lockbox/4-lockbox-request-details-azure-portal.png)


    核准要求之後，裝置磁片會解除鎖定，並可在支援會話中存取共用。

4. 支援工程師會解決上傳問題，然後停用支援會話。

解決問題之後，資料複製作業將會進行到完成。


## <a name="next-steps"></a>接下來的步驟

- [適用於 Microsoft Azure 的客戶加密箱](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)

<!--- [Approve, audit support access requests to VMs using Customer Lockbox for Azure](https://azure.microsoft.com/blog/approve-audit-support-access-requests-to-vms-using-customer-lockbox-for-azure/)-->


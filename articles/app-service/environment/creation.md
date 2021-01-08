---
title: 建立 App Service 環境
description: 瞭解如何建立 App Service 環境。
author: ccompy
ms.assetid: 7690d846-8da3-4692-8647-0bf5adfd862a
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 52a43874060748479beebc994bf0a9b663cde9dc
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020174"
---
# <a name="create-an-app-service-environment"></a>建立 App Service 環境

> [!NOTE]
> 本文說明 App Service 環境 v3 (preview) 
> 

[App Service 環境 (ASE) ][Intro]是 App Service 的單一租使用者部署，會插入您的 Azure 虛擬網路 (VNet) 。  ASEv3 只支援在 Vnet 的私人位址上公開應用程式。 在預覽期間建立 ASEv3 時，會將這些資源新增至您的訂用帳戶。

- App Service 環境
- 私人端點

ASE 的部署將需要使用兩個子網。  一個子網會保存私人端點。  此子網可用於 Vm 之類的其他專案。  另一個子網用於從 ASE 發出的輸出呼叫。  此子網不能用於 ASE 以外的任何其他以外的地方。 

## <a name="before-you-create-your-ase"></a>建立 ASE 之前

建立 ASE 之後，您就無法變更：

- 位置
- 訂用帳戶
- 資源群組
- 使用的 Azure 虛擬網路 (VNet) 
- 使用的子網
- 子網路大小
- ASE 的名稱

輸出子網必須夠大，才能容納您將擴充 ASE 的大小上限。 挑選夠大的子網，以支援您的最大調整需求，因為它在建立後即無法變更。 建議的大小為具有256位址的/24。

建立 ASE 之後，您可以在其中新增應用程式。 當您的 ASEv3 沒有 App Service 方案時，會有一筆費用，如同您在 ASE 中有 I1v2 App Service 方案的一個實例一樣。  

ASEv3 只會在選取區域中提供。 將會新增更多區域，因為預覽會隨著 GA 進行移動。 

## <a name="creating-an-ase-in-the-portal"></a>在入口網站中建立 ASE

1. 若要建立 ASEv3，請在 marketplace 中搜尋 **App Service 環境 (preview)**。  
2. 基本概念：選取訂用帳戶、選取或建立資源群組，然後輸入您的 ASE 名稱。  ASE 名稱也會用於 ASE 的網域尾碼。  如果您的 ASE 名稱是 *contoso* ，則網域尾碼將會是 *contoso.appserviceenvironment.net*。  此名稱將會自動設定在您的 Azure DNS 私人區域中，並將 ASE 部署到其中。 

    ![App Service 環境建立基本] 索引標籤](./media/creation/creation-basics.png)

3. 裝載：選取 OS 喜好設定、主機群組部署。 OS 喜好設定會指出您最初將在此 ASE 中用於應用程式的作業系統。 您可以在建立 ASE 之後，新增其他作業系統的應用程式。 主機群組部署是用來選取專用硬體。 透過 ASEv3，您可以選取 [已啟用]，然後進入專用硬體。 您需支付建立 ASE 的整個專用主機費用，然後以較低的 App Service 方案實例價格來收費。 

    ![App Service 環境裝載選項](./media/creation/creation-hosting.png)

4. 網路功能：選取或建立您的虛擬網路、選取或建立您的輸入子網、選取或建立您的輸出子網。 用於輸出的任何子網都必須是空的，並委派給 Microsoft. Web/hostingEnvironments。 如果您透過入口網站建立子網，則會自動為您委派子網。

    ![App Service 環境網路選取專案](./media/creation/creation-networking.png)

5. 檢查並建立：檢查您的設定是否正確，然後選取 [建立]。 您的 ASE 需要大約一小時的時間來建立。 

    ![App Service 環境審查和建立](./media/creation/creation-review.png)

ASE 建立完成之後，您可以在建立應用程式時將其選取為位置。 若要深入瞭解如何在新的 ASE 中建立應用程式，請參閱 [使用 App Service 環境][UsingASE]

## <a name="os-preference"></a>作業系統喜好設定
在 ASE 中，您可以有 Windows 應用程式、Linux 應用程式或兩者。 在 ASEv2 中，建立時選取的初始喜好設定會在 ASE 建立期間新增該 OS 的高可用性基礎結構。 若要新增其他作業系統的應用程式，請像平常一樣地建立應用程式，並選取您想要的作業系統。 在 ASEv3 中，這不會影響後端行為 appreciatively。  

## <a name="dedicated-hosts"></a>專用主機
ASE 通常會部署在布建在多租使用者虛擬程式上的 Vm 上。 如果您需要在專用系統（包括硬體）上部署，您可以將 ASE 布建到專用主機。 在初始 ASEv3 預覽版中，專用主機會成對。 如果區域允許，每個專用主機都會位於不同的可用性區域中。 以主機為基礎的專用 ASE 部署的定價方式與一般不同。 專用主機需要付費，然後針對每個 App Service 方案實例收取另一項費用。  

<!--Links-->
[Intro]: ./overview.md
[MakeASE]: ./creation.md
[ASENetwork]: ./networking.md
[UsingASE]: ./using.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/platform/alerts-log.md

---
title: 教學課程 - 為 Azure AD Domain Services 設定虛擬網路 | Microsoft Docs
description: 在本教學課程中，您將了解如何使用 Azure 入口網站為 Azure Active Directory Domain Services 受控網域建立和設定 Azure 虛擬網路子網路或網路對等互連。
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: ddb8f360304bdb41ae359f293af4d10b0afc6558
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2020
ms.locfileid: "96618411"
---
# <a name="tutorial-configure-virtual-networking-for-an-azure-active-directory-domain-services-managed-domain"></a>教學課程：為 Azure Active Directory Domain Services 受控網域設定虛擬網路

為了提供使用者和應用程式的連線能力，我們必須將 Azure Active Directory Domain Services (Azure AD DS) 受控網域部署至 Azure 虛擬網路子網路中。 此虛擬網路子網路只能用於 Azure 平台所提供的受控網域資源。

當您建立自己的 VM 和應用程式時，您不應將其部署至相同的虛擬網路子網路中。 此時您應建立應用程式，並將其部署至個別的虛擬網路子網路中，或對等互連至 Azure AD DS 虛擬網路的個別虛擬網路中。

本教學課程說明如何建立和設定專用的虛擬網路子網路，或如何將不同的網路對等互連至 Azure AD DS 受控網域的虛擬網路。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 了解將已加入網域的資源連線至 Azure AD DS 的虛擬網路連線選項
> * 在 Azure AD DS 虛擬網路中建立 IP 位址範圍和其他子網路
> * 設定將虛擬網路對等互連至 Azure AD DS 以外的網路

如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)再開始。

## <a name="prerequisites"></a>必要條件

若要完成此教學課程，您需要下列資源和權限：

* 有效的 Azure 訂用帳戶。
    * 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 您必須擁有 Azure AD 租用戶的 *全域管理員* 權限，才能設定 Azure AD DS。
* 您需要 Azure 訂用帳戶中的「參與者」權限，才能建立必要的 Azure AD DS 資源。
* 已在您的 Azure AD 租用戶中啟用並設定 Azure Active Directory Domain Services 受控網域。
    * 如有需要，第一個教學課程會引導您[建立並設定 Azure Active Directory Domain Services 受控網域][create-azure-ad-ds-instance]。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

在本教學課程中，您會使用 Azure 入口網站來建立和設定受控網域。 若要開始使用，請先登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="application-workload-connectivity-options"></a>應用程式工作負載連線選項

在上一個教學課程中，我們已建立一個受控網域，並使用虛擬網路的一些預設設定選項。 這些預設選項建立了 Azure 虛擬網路和虛擬網路子網路。 提供受控網域服務的 Azure AD DS 網域控制站會連線至此虛擬網路子網路。

當您建立和執行需要使用受控網域的 VM 時，必須要提供網路連線。 此網路連線可透過下列其中一種方式來提供：

* 在受控網域的虛擬網路中，建立額外的虛擬網路子網路。 您會在這個額外的子網路中建立和連接 VM。
    * 這些 VM 屬於相同的虛擬網路，因此它們能夠自動執行名稱解析，並與 Azure AD DS 網域控制站通訊。
* 設定從受控網域的虛擬網路到一或多個不同虛擬網路的 Azure 虛擬網路對等互連。 您會在這些個別的虛擬網路中建立和連接 VM。
    * 當您設定虛擬網路對等互連時，您也必須設定 DNS 設定，以對 Azure AD DS 網域控制站反向使用名稱解析。

通常，您只會使用前述其中一個網路連線選項。 此選擇通常取決您想要以何種方式管理不同的 Azure 資源。

* 如果您想要以一個資源群組來管理 Azure AD DS 和連線的 VM，您可以為 VM 另行建立虛擬網路子網路。
* 如果您想要個別管理 Azure AD DS 和後續任何連線的 VM，您可以使用虛擬網路對等互連。
    * 您也可以選擇使用虛擬網路對等互連，針對 Azure 環境中連線至現有虛擬網路的現有 VM 提供連線。

在本教學課程中，您只需要設定其中一個虛擬網路連線選項。

如需如何規劃和設定虛擬網路的詳細資訊，請參閱 [Azure Active Directory Domain Services 的網路考量][network-considerations]。

## <a name="create-a-virtual-network-subnet"></a>建立虛擬網路子網路

根據預設，在受控網域中建立的 Azure 虛擬網路會包含一個虛擬網路子網路。 此虛擬網路子網路應僅供 Azure 平台用來提供受控網域服務。 若要在此 Azure 虛擬網路中建立及使用您自己的 VM，請建立額外的子網路。

若要建立 VM 和應用程式工作負載的虛擬網路子網路，請完成下列步驟：

1. 在 Azure 入口網站中，選取受控網域的資源群組，例如 *myResourceGroup*。 從資源清單中選擇預設的虛擬網路，例如 *aadds-vnet*。
1. 在虛擬網路視窗的左側功能表中，選取 [位址空間]。 建立虛擬網路時，會使用預設子網路所使用的單一位址空間 *10.0.2.0/24*。

    將額外的 IP 位址範圍新增至虛擬網路。 此位址範圍的大小以及要使用的實際 IP 位址範圍，取決於已部署的其他網路資源。 IP 位址範圍不應與您 Azure 或內部部署環境中任何現有的位址範圍重疊。 請確定您的 IP 位址範圍大小足以容納預期要部署到子網路中的 VM 數目。

    在下列範例中，會新增額外的 IP 位址範圍 *10.0.3.0/24*。 在準備就緒時，選取 [儲存]。

    ![在 Azure 入口網站中新增額外的虛擬網路 IP 位址範圍](./media/tutorial-configure-networking/add-vnet-address-range.png)

1. 接下來，在虛擬網路視窗的左側功能表中選取 [子網路]，然後選擇 [+ 子網路] 以新增子網路。
1. 輸入子網路的名稱，例如 *workloads*。 如果您想要在先前的步驟中使用為虛擬網路設定的 IP 位址範圍子集，請視需要更新 **位址範圍**。 目前，請暫且將網路安全性群組、路由表、服務端點等選項保留為預設值。

    在下列範例中，會建立名為 *workloads* 的子網路，並使用 *10.0.3.0/24* IP 位址範圍：

    ![在 Azure 入口網站中新增額外的虛擬網路子網路](./media/tutorial-configure-networking/add-vnet-subnet.png)

1. 在準備就緒時，選取 [確定]。 建立虛擬網路子網路需要一些時間。

當您建立需要使用受控網域的 VM 時，請務必選取此虛擬網路子網路。 請勿在預設的 *aadds-subnet* 中建立 VM。 如果您選取不同的虛擬網路，除非您設定了虛擬網路對等互連，否則將無法使用網路連線和 DNS 解析連線至受控網域。

## <a name="configure-virtual-network-peering"></a>設定虛擬網路對等互連

您可以將現有的 Azure 虛擬網路用於 VM，或是保有獨立的受控網域虛擬網路。 若要使用受控網域，其他虛擬網路中的 VM 必須要有與 Azure AD DS 網域控制站通訊的途徑。 您可以使用 Azure 虛擬網路對等互連來提供此連線能力。

使用 Azure 虛擬網路對等互連時，不需要虛擬私人網路 (VPN) 裝置，即可將兩個虛擬網路連接在一起。 網路對等互連可讓您快速連接虛擬網路，並定義您整個 Azure 環境中的流量。

如需對等互連的詳細資訊，請參閱 [Azure 虛擬網路對等互連概觀][peering-overview]。

若要將虛擬網路對等互連至受控網域虛擬網路，請完成下列步驟：

1. 選擇為名為 *aadds-vnet* 的受控網域建立的預設虛擬網路。
1. 在虛擬網路視窗的左側功能表中，選取 [對等互連]。
1. 若要建立對等互連，請選取 [+ 新增]。 在下列範例中，預設的 *aadds-vnet* 會對等互連至名為 *myVnet* 的虛擬網路。 請使用您自己的值進行下列設定：

    * **從 aadds-vnet 到遠端虛擬網路的對等互連名稱**：這兩個網路的描述性識別碼，例如 *aadds-vnet-to-myvnet*
    * **虛擬網路部署類型**：*Resource Manager*
    * 訂用帳戶：您要對等互連之虛擬網路的訂用帳戶，例如 *Azure*
    * **虛擬網路**：您要對等互連的虛擬網路，例如 *myVnet*
    * **從 myVnet 到 aadds-vnet 的對等互連名稱**：這兩個網路的描述性識別碼，例如 *myvnet-to-aadds-vnet*

    ![在 Azure 入口網站中設定虛擬網路對等互連](./media/tutorial-configure-networking/create-peering.png)

    除非您的環境有特定需求，否則請保留虛擬網路存取或轉送流量的任何其他預設值，然後選取 [確定]。

1. 在 Azure AD DS 虛擬網路和您選取的虛擬網路上建立對等互連，需要幾分鐘的時間。 準備就緒時，**對等互連狀態** 會報告為「已連線」，如下列範例所示：

    ![Azure 入口網站中成功連線的對等互連網路](./media/tutorial-configure-networking/connected-peering.png)

在對等互連虛擬網路中的 VM 可以使用受控網域之前，請先將 DNS 伺服器設定為允許正確的名稱解析。

### <a name="configure-dns-servers-in-the-peered-virtual-network"></a>在對等互連的虛擬網路中設定 DNS 伺服器

若要讓對等互連虛擬網路中的 VM 和應用程式成功聯繫受控網域，必須要更新 DNS 設定。 Azure AD DS 網域控制站的 IP 位址必須設定為對等互連虛擬網路上的 DNS 伺服器。 有兩種方式可將網域控制站設定為對等互連虛擬網路的 DNS 伺服器：

* 設定 Azure 虛擬網路 DNS 伺服器，以使用 Azure AD DS 網域控制站。
* 設定在對等互連虛擬網路上使用的現有 DNS 伺服器，以使用條件式 DNS 轉送將查詢導向受控網域。 這些步驟會根據使用中現有的 DNS 伺服器而有所不同。

在本教學課程中，我們將設定 Azure 虛擬網路 DNS 伺服器，以將所有查詢導向至 Azure AD DS 網域控制站。

1. 在 Azure 入口網站中，選取對等互連虛擬網路的資源群組，例如 *myResourceGroup*。 從資源清單中選擇對等互連的虛擬網路，例如 *myVnet*。
1. 在虛擬網路視窗的左側功能表中，選取 [DNS 伺服器]。
1. 根據預設，虛擬網路會使用 Azure 提供的內建 DNS 伺服器。 請選擇使用 **自訂** DNS 伺服器。 輸入 Azure AD DS 網域控制站的 IP 位址，通常為 *10.0.2.4* 和 *10.0.2.5*。 請在入口網站中，從您受控網域的 [概觀] 視窗上確認這些 IP 位址。

    ![設定虛擬網路 DNS 伺服器，以使用 Azure AD DS 網域控制站](./media/tutorial-configure-networking/custom-dns.png)

1. 在準備就緒時，選取 [儲存]。 更新虛擬網路的 DNS 伺服器需要幾分鐘的時間。
1. 若要將更新的 DNS 設定套用至 VM，請重新啟動連線至對等互連虛擬網路的 VM。

當您建立需要使用受控網域的 VM 時，請務必選取此對等互連虛擬網路。 如果您選取不同的虛擬網路，則無法使用網路連線和 DNS 解析連線至受控網域。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 了解將已加入網域的資源連線至 Azure AD DS 的虛擬網路連線選項
> * 在 Azure AD DS 虛擬網路中建立 IP 位址範圍和其他子網路
> * 設定將虛擬網路對等互連至 Azure AD DS 以外的網路

若要查看此作用中的受控網域，請建立虛擬機器並將其加入網域。

> [!div class="nextstepaction"]
> [將 Windows Server 虛擬機器加入受控網域](join-windows-vm.md)

<!-- INTERNAL LINKS --> 
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[peering-overview]: ../virtual-network/virtual-network-peering-overview.md
[network-considerations]: network-considerations.md

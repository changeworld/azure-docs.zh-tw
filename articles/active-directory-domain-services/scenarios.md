---
title: Azure AD Domain Services 的常見部署案例 |Microsoft Docs
description: 瞭解 Azure Active Directory Domain Services 的一些常見案例和使用案例，以提供價值並滿足商務需求。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: 5d0035e7c87806012d13117ae5335b7de5f3c99d
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132290"
---
# <a name="common-use-cases-and-scenarios-for-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 的常見使用案例和情節

Azure Active Directory Domain Services （Azure AD DS）提供受控網域服務，例如加入網域、群組原則、輕量型目錄存取協定（LDAP），以及 Kerberos/NTLM 驗證。 Azure AD DS 與您現有的 Azure AD 租用戶整合，讓使用者能夠使用其現有認證登入。 您可以使用這些網域服務，而不需要在雲端中部署、管理和修補網域控制站，以提供更順暢的內部部署資源隨即轉移至 Azure。

本文概述一些常見的商務案例，其中 Azure AD DS 提供價值並符合這些需求。

## <a name="secure-administration-of-azure-virtual-machines"></a>Azure 虛擬機器的安全管理

為了讓您使用一組 AD 認證，Azure 虛擬機器（Vm）可以聯結至 Azure AD DS 受控網域。 這種方法可減少認證管理問題，例如維護每個 VM 上的本機系統管理員帳戶，或在環境之間個別的帳戶和密碼。

您也可以使用群組原則來管理和保護已加入 Azure AD DS 受控網域的 Vm。 必要的安全性基準可套用至 Vm，以根據公司安全性方針將其鎖定。 例如，您可以使用群組原則管理功能來限制可在 VM 上啟動的應用程式類型。

![流暢的管理 Azure 虛擬機器](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

讓我們來看一個常見的範例案例。 隨著伺服器和其他基礎結構的生命週期結束，Contoso 想要將目前裝載于內部部署的應用程式移至雲端。 其目前的 IT 標準規定，主控公司應用程式的伺服器必須已加入網域，並使用群組原則進行管理。 Contoso 的 IT 系統管理員想要加入 Azure 中部署的 Vm，讓系統管理更容易，因為使用者可以使用其公司認證進行登入。 加入網域時，也可以使用群組原則物件（Gpo）設定 Vm，以符合所需的安全性基準。 Contoso 不想要在 Azure 中部署、監視和管理自己的網域控制站。

Azure AD DS 非常適合這種使用案例。 Azure AD DS 受控網域可讓您將 Vm 加入網域、使用單一認證集，以及套用群組原則。 身為受控網域，您不需要自行設定和維護網域控制站。

### <a name="deployment-notes"></a>部署注意事項

下列部署考慮適用于此範例使用案例：

* Azure AD DS 受控網域預設會使用單一、一般的組織單位（OU）結構。 所有已加入網域的 Vm 都在單一 OU 中。 如有需要，您可以建立自訂 Ou。
* Azure AD DS 會針對使用者和電腦容器使用內建的 GPO。 如需其他控制，您可以建立自訂 Gpo，並將它們的目標設為自訂 Ou。
* Azure AD DS 支援基底 AD 電腦物件架構。 您無法擴充電腦物件的架構。

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-bind-authentication"></a>使用 LDAP 系結驗證的隨即轉移內部部署應用程式

在範例案例中，Contoso 有許多年前從 ISV 購買的內部部署應用程式。 應用程式目前是由 ISV 在維護模式下進行，要求應用程式的變更相當耗費資源。 此應用程式具有 web 架構的前端，會使用 web 表單來收集使用者認證，然後藉由對內部部署 AD DS 環境執行 LDAP 系結來驗證使用者。

![LDAP 繫結](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso 想要將此應用程式遷移至 Azure。 應用程式應該會繼續正常運作，不需要任何變更。 此外，使用者應該能夠使用其現有的公司認證來進行驗證，而不需要額外的訓練。 這對執行應用程式的使用者而言應該是透明的。

在此案例中，Azure AD DS 可讓應用程式在驗證過程中執行 LDAP 系結。 舊版內部部署應用程式可以隨即轉移至 Azure，並繼續順暢地驗證使用者，而不需要變更設定或使用者體驗。

### <a name="deployment-notes"></a>部署注意事項

下列部署考慮適用于此範例使用案例：

* 請確定應用程式不需要修改/寫入目錄。 不支援對 Azure AD DS 受控網域進行 LDAP 寫入存取。
* 您不能直接針對 Azure AD DS 受控網域變更密碼。 一般使用者可以使用 Azure AD 的自助密碼變更機制或對內部部署目錄變更其密碼。 這些變更會自動同步處理，並可在 Azure AD DS 受控網域中使用。

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-read-to-access-the-directory"></a>使用 LDAP 讀取來存取目錄的隨即轉移內部部署應用程式

就像先前的範例案例一樣，假設 Contoso 具有內部部署的企業營運（LOB）應用程式，這幾乎是十年前開發的。 此應用程式是目錄感知，其設計目的是要使用 LDAP 從 AD DS 讀取使用者的相關資訊/屬性。 應用程式不會修改屬性，或以其他方式寫入目錄。

Contoso 想要將此應用程式遷移至 Azure，並淘汰目前裝載此應用程式的過時內部部署硬體。 應用程式無法重寫以使用新式目錄 Api，例如以 REST 為基礎的 Microsoft Graph API。 您可以使用隨即轉移選項，將應用程式遷移至雲端執行，而不需要修改程式碼或重寫應用程式。

為協助進行此案例，Azure AD DS 可讓應用程式對受控網域執行 LDAP 讀取，以取得所需的屬性資訊。 應用程式不需要重新撰寫，因此，隨即轉移至 Azure 可讓使用者繼續使用應用程式，而不會發現執行的位置有變更。

### <a name="deployment-notes"></a>部署注意事項

下列部署考慮適用于此範例使用案例：

* 請確定應用程式不需要修改/寫入目錄。 不支援對 Azure AD DS 受控網域進行 LDAP 寫入存取。
* 請確定應用程式不需要自訂/擴充 Active Directory 架構。 Azure AD DS 中不支援架構延伸。

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure"></a>將內部部署服務或 daemon 應用程式遷移至 Azure

某些應用程式包含多個層級，其中其中一層需要對後端層（例如資料庫）執行已驗證的呼叫。 AD 服務帳戶通常用於這些案例中。 當您將應用程式隨即轉移至 Azure 時，Azure AD DS 可讓您以相同的方式繼續使用服務帳戶。 您可以選擇使用從內部部署目錄同步處理的相同服務帳戶來 Azure AD 或建立自訂 OU，然後在該 OU 中建立個別的服務帳戶。 無論使用哪一種方法，應用程式都會以相同的方式繼續運作，以對其他層和服務進行已驗證的呼叫。

![使用 WIA 的服務帳戶](./media/active-directory-domain-services-scenarios/wia-service-account.png)

在此範例案例中，Contoso 有一個自訂的軟體保存庫應用程式，其中包含 web 前端、SQL server 和後端 FTP 伺服器。 使用服務帳戶的 Windows 整合式驗證會向 FTP 伺服器驗證 web 前端。 並設定 Web 前端以服務帳戶的身分執行。 後端伺服器則設定為從服務帳戶授權對 Web 前端的存取權。 Contoso 不想在雲端部署和管理自己的網域控制站 Vm，將此應用程式移至 Azure。

在此案例中，裝載 web 前端、SQL server 和 FTP 伺服器的伺服器可以遷移至 Azure Vm，並加入 Azure AD DS 受控網域。 然後，Vm 就可以在其內部部署目錄中使用相同的服務帳戶來進行應用程式的驗證，這會透過 Azure AD 使用 Azure AD Connect 進行同步處理。

### <a name="deployment-notes"></a>部署注意事項

下列部署考慮適用于此範例使用案例：

* 請確定應用程式使用使用者名稱和密碼進行驗證。 Azure AD DS 不支援憑證或以智慧卡為基礎的驗證。
* 您不能直接針對 Azure AD DS 受控網域變更密碼。 一般使用者可以使用 Azure AD 的自助密碼變更機制或對內部部署目錄變更其密碼。 這些變更會自動同步處理，並可在 Azure AD DS 受控網域中使用。

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>Azure 中的 Windows Server 遠端桌面服務部署

您可以使用 Azure AD DS，為 Azure 中部署的遠端桌面伺服器提供受控網域服務。 如需此部署案例的詳細資訊，請參閱[如何將 Azure AD Domain Services 與您的 RDS 部署整合][windows-rds]。

## <a name="domain-joined-hdinsight-clusters-preview"></a>已加入網域的 HDInsight 叢集（預覽）

您可以設定 Azure HDInsight 叢集，此叢集已加入已啟用 Apache Ranger 的 Azure AD DS 受控網域。 您可以透過 Apache Ranger 建立和套用 Hive 原則，並允許使用者（例如資料科學家）使用 ODBC 工具（如 Excel 或 Tableau）連接到 Hive。 我們會繼續將其他工作負載（例如 HBase、Spark 和風暴）新增至已加入網域的 HDInsight。

如需此部署案例的詳細資訊，請參閱[如何設定已加入網域的 HDInsight][hdinsight]叢集

## <a name="next-steps"></a>後續步驟

若要開始使用，請[建立並設定 Azure Active Directory Domain Services 實例][tutorial-create-instance]

<!-- INTERNAL LINKS -->
[hdinsight]: ../hdinsight/domain-joined/apache-domain-joined-configure.md
[tutorial-create-instance]: tutorial-create-instance.md

<!-- EXTERNAL LINKS -->
[windows-rds]: /windows-server/remote/remote-desktop-services/rds-azure-adds

---
title: Azure AD 域服務的常見部署方案 |微軟文檔
description: 瞭解 Azure 活動目錄域服務的一些常見方案和用例，以提供價值並滿足業務需求。
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
ms.openlocfilehash: ac67ef64ca4850c6e805b5314ace856114d889a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77917225"
---
# <a name="common-use-cases-and-scenarios-for-azure-active-directory-domain-services"></a>Azure 活動目錄域服務的常見用例和方案

Azure 活動目錄域服務 （Azure AD DS） 提供託管域服務，如域聯接、群組原則、羽量級目錄訪問協定 （LDAP） 和 Kerberos / NTLM 身份驗證。 Azure AD DS 與您現有的 Azure AD 租用戶整合，讓使用者能夠使用其現有認證登入。 使用這些域服務無需在雲中部署、管理和修補網域控制站，從而更順暢地將本地資源提升到 Azure。

本文概述了 Azure AD DS 提供價值並滿足這些需求的一些常見業務方案。

## <a name="secure-administration-of-azure-virtual-machines"></a>安全管理 Azure 虛擬機器

為了允許您使用一組 AD 憑據，Azure 虛擬機器 （VM） 可以加入到 Azure AD DS 託管域。 此方法可減少憑據管理問題，例如在每個 VM 上維護本地管理員帳戶，或在環境之間單獨維護帳戶和密碼。

也可以使用群組原則管理和保護加入 Azure AD DS 託管域的 VM。 所需的安全基線可以應用於 VM，以便根據公司安全準則將其鎖定。 例如，可以使用群組原則管理功能來限制可在 VM 上啟動的應用程式類型。

![流暢的管理 Azure 虛擬機器](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

讓我們看一個常見的示例方案。 隨著伺服器和其他基礎架構的生命週期結束，Contoso 希望將當前託管在本地的應用程式遷移到雲。 他們當前的 IT 標準要求託管公司應用程式的伺服器必須使用群組原則加入和管理域。 Contoso 的 IT 管理員更願意將 Azure 中部署的 VM 域域化，以便簡化管理，因為使用者可以使用其公司憑據登錄。 加入域後，還可以將 VM 配置為使用群組原則物件 （GPO） 符合所需的安全基線。 Contoso 不希望在 Azure 中部署、監視和管理自己的網域控制站。

Azure AD DS 非常適合此用例。 Azure AD DS 託管域允許您加入 VM、使用一組憑據以及應用群組原則。 作為託管域，您不必自己配置和維護網域控制站。

### <a name="deployment-notes"></a>部署注意事項

以下部署注意事項適用于此示例用例：

* 預設情況下，Azure AD DS 託管域使用單個平面組織單元 （OU） 結構。 所有加入域的 VM 都在單個 OU 中。 如果需要，您可以創建自訂 O。
* Azure AD DS 對使用者和電腦容器使用內置 GPO。 對於其他控制項，您可以創建自訂 GPO 並將其定位到自訂 O。
* Azure AD DS 支援基本 AD 電腦物件架構。 無法擴展電腦物件的架構。

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-bind-authentication"></a>使用 LDAP 綁定身份驗證的提升和移動本地應用程式

作為示例方案，Contoso 具有多年前從 ISV 購買的本地應用程式。 ISV 當前處於維護模式，請求對應用程式的更改非常昂貴。 此應用程式具有基於 Web 的前端，該前端使用 Web 表單收集使用者憑據，然後通過執行到本地 AD DS 環境的 LDAP 綁定對使用者進行身份驗證。

![LDAP 繫結](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso 希望將此應用程式遷移到 Azure。 應用程式應繼續以正常方式工作，無需進行任何更改。 此外，使用者應該能夠使用其現有的公司憑據進行身份驗證，而無需進行其他培訓。 對於運行應用程式的最終使用者，它應該是透明的。

對於此方案，Azure AD DS 允許應用程式作為身份驗證過程的一部分執行 LDAP 綁定。 舊式本地應用程式可以提升和移入 Azure，並繼續無縫地對使用者進行身份驗證，而無需更改配置或使用者體驗。

### <a name="deployment-notes"></a>部署注意事項

以下部署注意事項適用于此示例用例：

* 確保應用程式不需要修改/寫入目錄。 不支援對 Azure AD DS 託管域的 LDAP 寫入訪問。
* 不能針對 Azure AD DS 託管域直接更改密碼。 一般使用者可以使用 Azure AD 的自助密碼變更機制或對內部部署目錄變更其密碼。 然後，這些更改將自動同步並在 Azure AD DS 託管域中可用。

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-read-to-access-the-directory"></a>使用 LDAP 讀取存取目錄的提升和移動本地應用程式

與前面的示例方案一樣，假設 Contoso 具有近 10 年前開發的本地業務線 （LOB） 應用程式。 此應用程式具有目錄感知功能，旨在使用 LDAP 從 AD DS 讀取有關使用者的資訊/屬性。 應用程式不修改屬性或以其他方式寫入目錄。

Contoso 希望將此應用程式遷移到 Azure 並停用當前承載此應用程式的老化的本地硬體。 無法重寫應用程式以使用現代目錄 API，如基於 REST 的 Microsoft 圖形 API。 需要一個提升和移動選項，其中應用程式可以遷移到雲中運行，而無需修改代碼或重寫應用程式。

為了説明執行此方案，Azure AD DS 允許應用程式針對託管域執行 LDAP 讀取，以獲得所需的屬性資訊。 應用程式不需要重寫，因此，在 Azure 中提升和移動允許使用者繼續使用應用程式，而無需意識到其運行位置有變化。

### <a name="deployment-notes"></a>部署注意事項

以下部署注意事項適用于此示例用例：

* 確保應用程式不需要修改/寫入目錄。 不支援對 Azure AD DS 託管域的 LDAP 寫入訪問。
* 確保應用程式不需要自訂/擴展的活動目錄架構。 Azure AD DS 不支援架構擴展。

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure"></a>將本機服務或守護進程應用程式遷移到 Azure

某些應用程式包括多個層，其中其中一個層需要對後端層（如資料庫）執行經過身份驗證的調用。 在這些方案中，AD 服務帳戶通常使用。 將應用程式提升到 Azure 時，Azure AD DS 允許您繼續以相同的方式使用服務帳戶。 您可以選擇使用從本地目錄同步到 Azure AD 的相同服務帳戶，或者創建自訂 OU，然後在該 OU 中創建單獨的服務帳戶。 使用任一方法，應用程式繼續以相同的方式對其他層和服務進行身份驗證調用。

![使用 WIA 的服務帳戶](./media/active-directory-domain-services-scenarios/wia-service-account.png)

在此示例中，Contoso 具有一個自訂的軟體保存庫應用程式，該應用程式包括 Web 前端、SQL 伺服器和後端 FTP 伺服器。 使用服務帳戶的 Windows 集成身份驗證對 FTP 伺服器的 Web 前端進行身份驗證。 並設定 Web 前端以服務帳戶的身分執行。 後端伺服器則設定為從服務帳戶授權對 Web 前端的存取權。 Contoso 不希望在雲中部署和管理自己的網域控制站 VM 以將此應用程式移動到 Azure。

對於此方案，託管 Web 前端、SQL 伺服器和 FTP 伺服器的伺服器可以遷移到 Azure VM 並加入到 Azure AD DS 託管域。 然後，VM 可以在其本地目錄中使用相同的服務帳戶來用於應用的身份驗證目的，該帳戶使用 Azure AD 連接通過 Azure AD 同步。

### <a name="deployment-notes"></a>部署注意事項

以下部署注意事項適用于此示例用例：

* 確保應用程式使用使用者名和密碼進行身份驗證。 Azure AD DS 不支援基於證書或智慧卡的身份驗證。
* 不能針對 Azure AD DS 託管域直接更改密碼。 一般使用者可以使用 Azure AD 的自助密碼變更機制或對內部部署目錄變更其密碼。 然後，這些更改將自動同步並在 Azure AD DS 託管域中可用。

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>Azure 中的 Windows 伺服器遠端桌面服務部署

可以使用 Azure AD DS 向 Azure 中部署的遠端桌面伺服器提供託管域服務。 有關此部署方案的詳細資訊，請參閱[如何將 Azure AD 域服務與 RDS 部署集成][windows-rds]。

## <a name="domain-joined-hdinsight-clusters"></a>已加入網域的 HDInsight 叢集

您可以設置 Azure HDInsight 群集，該群集已連接到啟用 Apache Ranger 的 Azure AD DS 託管域。 您可以通過 Apache Ranger 創建和應用 Hive 策略，並允許使用者（如資料科學家）使用基於 ODBC 的工具（如 Excel 或 Tableau）連接到 Hive。 我們繼續努力向加入域的 HDInsight 添加其他工作負載，如 HBase、Spark 和 Storm。

有關此部署方案的詳細資訊，請參閱[如何配置加入域的 HDInsight 群集][hdinsight]

## <a name="next-steps"></a>後續步驟

要開始，[請創建和配置 Azure 活動目錄域服務實例][tutorial-create-instance]

<!-- INTERNAL LINKS -->
[hdinsight]: ../hdinsight/domain-joined/apache-domain-joined-configure.md
[tutorial-create-instance]: tutorial-create-instance.md

<!-- EXTERNAL LINKS -->
[windows-rds]: /windows-server/remote/remote-desktop-services/rds-azure-adds

---
title: 使用 ARM 建立 ILB ASE
description: 了解如何使用 Azure Resource Manager 範本建立具有內部負載平衡器 (ILB ASE) 的 App Service 環境。 將您的應用程式完全與網際網路隔離。
author: ccompy
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.topic: quickstart
ms.date: 09/16/2020
ms.author: ccompy
ms.custom: mvc, seodec18
ms.openlocfilehash: 1bda52227737b082927dd1449fa6469cf849ff15
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91273257"
---
# <a name="create-and-use-an-internal-load-balancer-app-service-environment"></a>建立及使用內部負載平衡器 App Service 環境 

Azure App Service 環境 (ASE) 是將 Azure App Service 部署到客戶 Azure 虛擬網路 (VNet) 中子網路的一種部署。 有二種方法可以部署 App Service Environment (ASE)： 

- 使用外部 IP 位址上的 VIP，通常稱為「外部 ASE」。
- 使用內部 IP 位址上的 VIP，通常稱為 「ILB ASE」，因為內部端點是內部負載平衡器 (ILB)。 

本文說明如何建立 ILB ASE。 如需 ASE 的概觀，請參閱 [App Service Environment 簡介][Intro]。 若要深了解如何建立外部 ASE，請參閱[建立外部 ASE][MakeExternalASE]。

## <a name="overview"></a>概觀 

您可以使用網際網路可存取端點或是您的 VNet 中的 IP 位址來部署 ASE。 為了將 IP 位址設定為 VNet 位址，ASE 必須與 ILB 一起部署。 在部署 ASE 與 ILB 時，您必須提供您的 ASE 名稱。 ASE 名稱使用於您的 ASE 中應用程式的網域尾碼。  ILB ASE 的網域尾碼是 &lt;ASE 名稱&gt;.appserviceenvironment.net。 在 ILB ASE 中製作的應用程式不會放在公用 DNS 中。 

舊版的 ILB ASE 會要求您提供網域尾碼和預設憑證，以便進行 HTTPS 連線。 網域尾碼不再於 ILB ASE 建立時收集，而且也不會再收集預設憑證。 當您立即建立 ILB ASE 時，預設憑證是由 Microsoft 提供並受到瀏覽器信任。 您仍可在 ASE 中的應用程式上設定自訂網域名稱，並在這些自訂網域名稱上設定憑證。 

使用 ILB ASE，您可以執行以下動作：

-   在您可透過端對端或 ExpressRoute 存取的雲端，安全地裝載內部網路應用程式。
-   使用 WAF 裝置保護應用程式
-   在雲端裝載未在公用 DNS 伺服器中列出的應用程式。
-   建立與網際網路隔離，且您的前端 app 可以安全地與之整合的後端應用程式。

### <a name="disabled-functionality"></a>已停用的功能 ###

使用 ILB ASE 時，有一些動作您無法執行：

-   使用以 IP 為主的 SSL。
-   將 IP 位址指派給特定應用程式。
-   透過 Azure 入口網站購買憑證並搭配應用程式使用。 您可以直接透過「憑證授權單位」取得憑證並搭配您的應用程式使用。 您無法透過 Azure 入口網站取得它們。

## <a name="create-an-ilb-ase"></a>建立 ILB ASE ##

若要建立 ILB ASE：

1. 在 Azure 入口網站中，選取 [建立資源]   > [Web]   > [App Service 環境]  。

2. 選取您的訂用帳戶。

3. 選取或建立資源群組。

4. 輸入您的 App Service 環境名稱。

5. 選取內部虛擬 IP 類型。

    ![ASE 建立](media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase.png)

> [!NOTE]
> App Service 環境名稱不能超過 37 個字元。

6. 選取網路

7. 選取或建立虛擬網路。 如果您在此建立新的 VNet，將會以 192.168.250.0/23 的位址範圍定義。 若要建立具有不同位址範圍的 VNet，或在與 ASE 不同的資源群組中建立 VNet，請使用 Azure 虛擬網路建立入口網站。 

8. 選取或建立空的子網路。 如果您想要選取子網路，它必須是空的而且未委派。 ASE 建立之後，便無法變更子網路大小。 建議的大小是 `/24`，具有 256 個位址，而且可以處理最大大小的 ASE 和任何縮放需求。 

    ![ASE 網路][1]

7. 選取 [檢閱並建立]  ，然後選取 [建立]  。


## <a name="create-an-app-in-an-ilb-ase"></a>在 ILB ASE 中建立應用程式： ##

在 ILB ASE 中建立應用程式的做法，與在 ASE 中建立應用程式相同。

1. 在 Azure 入口網站中，選取 [建立資源]   > [Web]   > [Web 應用程式]  。

1. 輸入應用程式的名稱。

1. 選取訂用帳戶。

1. 選取或建立資源群組。

1. 選取您的 [發佈]、[執行階段堆疊] 和 [作業系統]。

1. 選取位於現有 ILB ASE 的位置。  您也可以選取 [隔離的 App Service] 方案，以在應用程式建立期間建立新的 ASE。 如果您想要建立新的 ASE，請選取您要在其中建立 ASE 的區域。

1. 選取或建立 App Service 方案。 

1. 選取 [檢閱並建立]  ，然後在準備就緒時選取 [建立]  。

### <a name="web-jobs-functions-and-the-ilb-ase"></a>Web 工作、函式和 ILB ASE 

ILB ASE 支援函式和 Web 工作，但若要讓入口網站可以使用，您必須具有 SCM 網站的網路存取。  這表示您的瀏覽器必須在主機上，或是在虛擬網路中或已連線到虛擬網路。 如果 ILB ASE 的網域名稱結尾不是 *appserviceenvironment.net*，您就必須讓您的瀏覽器信任您的 scm 網站所使用的 HTTPS 憑證。

## <a name="dns-configuration"></a>DNS 組態 

當您使用外部 ASE 時，在 ASE 中建立的應用程式會向 Azure DNS 註冊。 外部 ASE 中不會有任何額外的步驟，讓您的應用程式可公開使用。 使用 ILB ASE 時，您必須管理您自己的 DNS。 您可以在自己的 DNS 伺服器中或使用 Azure DNS 私人區域執行此動作。

若要使用 ILB ASE 在自己的 DNS 伺服器中設定 DNS：

1. 為 <ASE name>.appserviceenvironment.net 建立一個區域
2. 在該區域中建立一個指向 ILB IP 位址的 A 記錄
3. 在該區域中建立一個將 @ 指向 ILB IP 位址的 A 記錄
4. 在名為 scm 的 <ASE name>.appserviceenvironment.net 中建立一個區域
5. 在 scm 區域中建立一個將 * 指向 ILB IP 位址的 A 記錄

若要在 Azure DNS 私人區域中設定 DNS：

1. 建立名為 <ASE name>.appserviceenvironment.net 的 Azure DNS 私人區域
2. 在該區域中建立一個指向 ILB IP 位址的 A 記錄
3. 在該區域中建立一個將 @ 指向 ILB IP 位址的 A 記錄
4. 在該區域中建立一個將 *.scm 指向 ILB IP 位址的 A 記錄

ASE 預設網域尾碼的 DNS 設定並不會將您的應用程式限制為僅供那些名稱存取。 在 ILB ASE 中，您可以在應用程式上設定自訂網域名稱，而不會進行任何驗證。 如果您想要建立名為 contoso.net 的區域，則可以這麼做，並將其指向 ILB IP 位址。 自訂網域名稱適用於應用程式要求，但不適用於 scm 網站。 scm 網站僅能在 <appname>.scm.<asename>.appserviceenvironment.net 上使用。

名為 <asename>.appserviceenvironment.net 的區域是全域唯一的。 在 2019 年 5月之前，客戶可以指定 ILB ASE 的網域尾碼。 如果想要使用 contoso.com 做為網域尾碼，您可以這麼做，這會包含 scm 網站。 該模型會有挑戰，包括管理預設 SSL 憑證、缺少與 scm 網站的單一登入，以及使用萬用字元憑證的需求。 ILB ASE 預設憑證升級程序也會造成干擾，並導致應用程式重新啟動。 為了解決這些問題，ILB ASE 行為已變更為根據 ASE 名稱和搭配 Microsoft 擁有的尾碼來使用網域尾碼。 ILB ASE 行為的變更只會影響 2019 年 5 月之後所做的 ILB ASE。 預先存在的 ILB ASE 仍然必須管理 ASE 的預設憑證和其 DNS 組態。

## <a name="publish-with-an-ilb-ase"></a>使用 ILB ASE 發佈

每一個建立的應用程式，都有兩個端點。 在 ILB ASE 中，您有 *&lt;應用程式名稱&gt;.&lt;ILB ASE 網域&gt;* 和 *&lt;應用程式名稱&gt;.scm.&lt;ILB ASE 網域&gt;* 。 

SCM 網站名稱會帶您前往 Azure 入口網站中的 Kudu 主控台，稱為 [進階入口網站]  。 Kudu 主控台可讓您檢視環境變數、探索磁碟、使用主控台等等。 如需詳細資訊，請參閱 [Azure App Service 的 Kudu 主控台][Kudu]。 

網際網路型的 CI 系統 (如 GitHub 和 Azure DevOps)，依然可以搭配 ILB ASE 使用，前提是組建代理程式可以存取網際網路，而且位在與 ILB ASE 相同的網路中。 對於 Azure DevOps，如果建立組建代理程式的 VNET 與 ILB ASE 相同 (子網路不同沒關係)，就能從 Azure DevOps git 提取程式碼，並部署到 ILB ASE。 如果您不想要建立自己的組建代理程式，便需要使用採用提取模型的 CI 系統，如 Dropbox。

ILB ASE 中應用程式的發佈端點會使用用來建立 ILB ASE 的網域。 在應用程式的發行設定檔中，以及應用程式的入口網站刀鋒視窗中可以看到這個網域 (在 [概觀]   >  [基本資訊]  以及 [屬性]  中)。 如果您的 ILB ASE 具有網域尾碼 *&lt;ASE 名稱&gt;.appserviceenvironment.net*，且應用程式名為 *mytest*，則要在 FTP 中使用 *mytest.&lt;ASE 名稱&gt;.appserviceenvironment.net*，在 Web 部署中使用 *mytest.scm.contoso.net*。

## <a name="configure-an-ilb-ase-with-a-waf-device"></a>設定具有 WAF 裝置的 ILB ASE ##

您可以結合 Web 應用程式防火牆 (WAF) 裝置與您的 ILB ASE，只公開您想要連到網際網路的應用程式，而其餘的應用程式只能在 VNet 中存取。 不說別的，這可讓您建置安全的多層式應用程式。

若要深入了解如何設定 ILB ASE 與 WAF 裝置，請參閱[使用 App Service Environment 設定 Web 應用程式防火牆][ASEWAF]。 此文章說明如何搭配使用您的 ASE 與 Barracuda 虛擬應用裝置。 另一個選項是使用 Azure 應用程式閘道。 應用程式閘道會使用 OWASP 核心規則來保護後面的任何應用程式。 如需有關應用程式閘道的詳細資訊，請參閱 [Azure Web 應用程式防火牆簡介][AppGW]。

## <a name="ilb-ases-made-before-may-2019"></a>在 2019 年 5 月前製作的 ILB ASE

在 2019 年 5 月前製作的 ILB ASE 需要您在 ASE 建立期間設定網域尾碼。 您也需要上傳以該網域尾碼為基礎的預設憑證。 此外，使用較舊的 ILB ASE，您就無法使用該 ILB ASE 中的應用程式，對 Kudu 主控台執行單一登入。 針對較舊的 ILB ASE 設定 DNS 時，您需要在符合您網域尾碼的區域中設定萬用字元 A 記錄。 

## <a name="get-started"></a>開始使用 ##

* 若要開始使用ASE，請參閱 [App Service Environment 簡介][Intro]。 

<!--Image references-->
[1]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-network.png
[2]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-webapp.png
[5]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-ipaddresses.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../overview.md
[mobileapps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[customdomain]: ../app-service-web-tutorial-custom-domain.md
[linuxapp]: ../overview.md#app-service-on-linux

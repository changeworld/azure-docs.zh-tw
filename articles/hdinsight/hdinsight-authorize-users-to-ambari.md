---
title: 授權 Ambari 檢視的使用者 - Azure HDInsight
description: 如何在啟用 ESP 的情況下，管理 HDInsight 叢集的 Ambari 使用者和群組權限。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: b1cd6d93ff45d7fb40ae7ca1874343486bd0b8cb
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547923"
---
# <a name="authorize-users-for-apache-ambari-views"></a>授權 Apache Ambari 檢視的使用者

[啟用企業安全性套件 (ESP) 的 HDInsight 叢集](./domain-joined/hdinsight-security-overview.md)可提供企業級功能，包括以 Azure Active Directory 為基礎的驗證。 您可以將已新增至已獲得存取權之 Azure AD 群組的[新使用者同步](hdinsight-sync-aad-users-to-cluster.md)至叢集，讓這些特定使用者能夠執行特定動作。 ESP HDInsight 叢集和標準 HDInsight 叢集都支援在 [Apache Ambari](https://ambari.apache.org/) 中運用使用者、群組及權限。

Active Directory 使用者可以使用其網域認證來登入叢集節點。 他們也可以使用其網域認證來驗證叢集與其他已核准的端點（例如， [色調](https://gethue.com/)、Ambari VIEWS、ODBC、JDBC、POWERSHELL 和 REST api）的互動。

> [!WARNING]  
> 請勿變更以 Linux 為基礎之 HDInsight 叢集上的 Ambari 看門狗 (hdinsightwatchdog) 密碼。 變更密碼會破壞在叢集上使用指令碼動作或執行調整作業的能力。

如果您尚未這麼做，請依照[這些指示](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)來佈建新 ESP 叢集。

## <a name="access-the-ambari-management-page"></a>存取 Ambari 管理頁面

若要移至 **Ambari 管理頁面** ，請在 [Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md) 上瀏覽至 `https://CLUSTERNAME.azurehdinsight.net`。 輸入您建立叢集時所定義的叢集系統管理員使用者名稱和密碼。 接著，從 Ambari 儀表板中，選取 [admin] \(系統管理員\)  功能表底下的 [Manage Ambari] \(管理 Ambari\)  ：

![Apache Ambari 儀表板管理](./media/hdinsight-authorize-users-to-ambari/manage-apache-ambari.png)

## <a name="add-users"></a>加入使用者

### <a name="add-users-through-the-portal"></a>透過入口網站新增使用者

1. 從 [管理] 頁面選取 [ **使用者** ]。

    ![Apache Ambari 管理頁面使用者](./media/hdinsight-authorize-users-to-ambari/apache-ambari-management-page-users.png)

1. 選取 [ **+ 建立本機使用者** ]。

1. 提供使用者 **名稱** 和 **密碼** 。 選取 [ **儲存** ]。

### <a name="add-users-through-powershell"></a>透過 PowerShell 新增使用者

將 `CLUSTERNAME` 、 `NEWUSER` 和取代 `PASSWORD` 為適當的值，以編輯下列變數。

```powershell
# Set-ExecutionPolicy Unrestricted

# Begin user input; update values
$clusterName="CLUSTERNAME"
$user="NEWUSER"
$userpass='PASSWORD'
# End user input

$adminCredentials = Get-Credential -UserName "admin" -Message "Enter admin password"

$clusterName = $clusterName.ToLower()
$createUserUrl="https://$($clusterName).azurehdinsight.net/api/v1/users"

$createUserBody=@{
    "Users/user_name" = "$user"
    "Users/password" = "$userpass"
    "Users/active" = "$true"
    "Users/admin" = "$false"
} | ConvertTo-Json

# Create user
$statusCode =
Invoke-WebRequest `
    -Uri $createUserUrl `
    -Credential $adminCredentials `
    -Method POST `
    -Headers @{"X-Requested-By" = "ambari"} `
    -Body $createUserBody | Select-Object -Expand StatusCode

if ($statusCode -eq 201) {
    Write-Output "User is created: $user"
}
else
{
    Write-Output 'User is not created'
    Exit
}

$grantPrivilegeUrl="https://$($clusterName).azurehdinsight.net/api/v1/clusters/$($clusterName)/privileges"

$grantPrivilegeBody=@{
    "PrivilegeInfo" = @{
        "permission_name" = "CLUSTER.USER"
        "principal_name" = "$user"
        "principal_type" = "USER"
    }
} | ConvertTo-Json

# Grant privileges
$statusCode =
Invoke-WebRequest `
    -Uri $grantPrivilegeUrl `
    -Credential $adminCredentials `
    -Method POST `
    -Headers @{"X-Requested-By" = "ambari"} `
    -Body $grantPrivilegeBody | Select-Object -Expand StatusCode

if ($statusCode -eq 201) {
    Write-Output 'Privilege is granted'
}
else
{
    Write-Output 'Privilege is not granted'
    Exit
}

Write-Host "Pausing for 100 seconds"
Start-Sleep -s 100

$userCredentials = "$($user):$($userpass)"
$encodedUserCredentials = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($userCredentials))
$zookeeperUrlHeaders = @{ Authorization = "Basic $encodedUserCredentials" }
$getZookeeperurl="https://$($clusterName).azurehdinsight.net/api/v1/clusters/$($clusterName)/services/ZOOKEEPER/components/ZOOKEEPER_SERVER"

# Perform query with new user
$zookeeperHosts =
Invoke-WebRequest `
    -Uri $getZookeeperurl `
    -Method Get `
    -Headers $zookeeperUrlHeaders

Write-Output $zookeeperHosts
```

### <a name="add-users-through-curl"></a>透過捲曲新增使用者

將 `CLUSTERNAME` 、 `ADMINPASSWORD` 、 `NEWUSER` 和取代 `USERPASSWORD` 為適當的值，以編輯下列變數。 腳本的設計目的是要搭配 bash 來執行。 Windows 命令提示字元需要稍微修改。

```bash
export clusterName="CLUSTERNAME"
export adminPassword='ADMINPASSWORD'
export user="NEWUSER"
export userPassword='USERPASSWORD'

# create user
curl -k -u admin:$adminPassword -H "X-Requested-By: ambari" -X POST \
-d "{\"Users/user_name\":\"$user\",\"Users/password\":\"$userPassword\",\"Users/active\":\"true\",\"Users/admin\":\"false\"}" \
https://$clusterName.azurehdinsight.net/api/v1/users

echo "user created: $user"

# grant permissions
curl -k -u admin:$adminPassword -H "X-Requested-By: ambari" -X POST \
-d '[{"PrivilegeInfo":{"permission_name":"CLUSTER.USER","principal_name":"'$user'","principal_type":"USER"}}]' \
https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/privileges

echo "Privilege is granted"

echo "Pausing for 100 seconds"
sleep 10s

# perform query using new user account
curl -k -u $user:$userPassword -H "X-Requested-By: ambari" \
-X GET "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER"
```

## <a name="grant-permissions-to-apache-hive-views"></a>授與對 Apache Hive 檢視的權限

Ambari 隨附 [Apache Hive](https://hive.apache.org/) 和 [Apache TEZ](https://tez.apache.org/) 等等的檢視執行個體。 若要授與對一或多個 Hive 檢視執行個體的存取權，請移至 **Ambari 管理頁面** 。

1. 從管理頁面中，選取左邊 [檢視] \(Views\)  功能表標題底下的 [檢視] \(Views\)  連結。

    ![Apache Ambari views view 連結](./media/hdinsight-authorize-users-to-ambari/apache-ambari-views-link.png)

2. 在 [檢視] \(Views\) 頁面上，展開 [HIVE]  資料列。 將 Hive 服務新增到叢集時，會建立一個預設的 Hive 檢視。 您也可以視需要建立更多 Hive 檢視執行個體。 選取一個 Hive 檢視：

    ![HDInsight 視圖-Apache Hive 視圖](./media/hdinsight-authorize-users-to-ambari/views-apache-hive-view.png)

3. 向下捲動到 [檢視] \(Views\) 頁面底部。 在 [權限] \(Permissions\)  區段底下，您有兩個選項來授與網域使用者對檢視的權限：

**Grant permission to these users (將權限授與這些使用者)** ![Grant permission to these users (將權限授與這些使用者)](./media/hdinsight-authorize-users-to-ambari/hdi-add-user-to-view.png)

**Grant permission to these groups (將權限授與這些群組)** ![Grant permission to these groups (將權限授與這些群組)](./media/hdinsight-authorize-users-to-ambari/add-group-to-view-permission.png)

1. 若要新增使用者，請選取 [Add User] \(新增使用者\)  按鈕。

   * 開始輸入使用者名稱，您將會看到先前已定義之名稱的下拉式清單。

     ![Apache Ambari 使用者自動完成](./media/hdinsight-authorize-users-to-ambari/ambari-user-autocomplete.png)

   * 選取使用者名稱，或完成使用者名稱輸入。 若要新增此使用者名稱作為新使用者，請選取 [New] \(新增\)  按鈕。

   * 若要儲存您的變更，請選取 **藍色核取方塊** 。

     ![Apache Ambari 授與使用者許可權](./media/hdinsight-authorize-users-to-ambari/user-entered-permissions.png)

1. 若要新增群組，請選取 [Add Group] \(新增群組\)  按鈕。

   * 開始輸入群組名稱。 不論是選取現有的群組名稱，還是新增新的群組，程序都與新增使用者相同。
   * 若要儲存您的變更，請選取 **藍色核取方塊** 。

     ![Apache Ambari 授與許可權](./media/hdinsight-authorize-users-to-ambari/ambari-group-entered.png)

當您想要指派權限給使用者來使用該檢視，但不想要讓使用者成為具有額外權限之群組的成員時，將使用者直接新增到檢視會相當有用。 若要降低系統管理額外負擔，將權限指派給群組可能會是更簡單的方式。

## <a name="grant-permissions-to-apache-tez-views"></a>授與對 Apache TEZ 檢視的權限

[Apache TEZ](https://tez.apache.org/) 檢視執行個體可讓使用者對所有 Tez 作業 (由 [Apache Hive](https://hive.apache.org/) 查詢和 [Apache Pig](https://pig.apache.org/) 指令碼所提交) 進行監視和偵錯。 佈建叢集時，會建立一個預設的 Tez 檢視執行個體。

若要將使用者和群組指派給 Tez 檢視執行個體，請依照先前所述，展開 [Views] \(檢視\) 頁面上的 [TEZ] **TEZ** 資料列。

![HDInsight 視圖-Apache Tez 視圖](./media/hdinsight-authorize-users-to-ambari/views-apache-tez-view.png)

若要新增使用者或群組，請重複上一節中的步驟 3 到 5。

## <a name="assign-users-to-roles"></a>將使用者指派給角色

使用者和群組有 5 個安全性角色，依存取權限由高至低列出：

* 叢集系統管理員
* 叢集操作員
* 服務管理員
* 服務操作員
* 叢集使用者

若要管理角色，請移至  功能表群組內的 [Roles] \(角色\)  連結。

![Apache Ambari 角色功能表連結](./media/hdinsight-authorize-users-to-ambari/cluster-roles-menu-link.png)

若要查看賦予每個角色的權限清單，請按一下 [Roles] \(角色\) 頁面上 [Roles] \(角色\)  資料表標頭旁邊的藍色問號。

![Apache Ambari 角色功能表連結許可權](./media/hdinsight-authorize-users-to-ambari/roles-menu-permissions.png "Apache Ambari 角色功能表連結許可權")

在此頁面上，有兩個不同的檢視，可供您用來管理使用者和群組的角色：[Block] \(區塊\) 和 [List] \(清單\)。

### <a name="block-view"></a>[區塊] 檢視

[Block] \(區塊\) 檢視會以每個角色自己一列的方式顯示每個角色，並如先前所述，會提供 [Assign roles to these users] \(指派角色給這些使用者\)  和 [Assign roles to these groups] \(指派角色給這些群組\)  選項。

![Apache Ambari 角色封鎖視圖](./media/hdinsight-authorize-users-to-ambari/ambari-roles-block-view.png)

### <a name="list-view"></a>清單檢視

[List] \(清單\) 檢視提供快速編輯功能，分成兩個類別：[Users] \(使用者\) 和 [Groups] \(群組\)。

* [List] \(清單\) 檢視的 [Users] \(使用者\) 類別會顯示所有使用者的清單，可讓您從下拉式清單中選取每個使用者的角色。

    ![Apache Ambari 角色清單視圖-使用者](./media/hdinsight-authorize-users-to-ambari/roles-list-view-users.png)

* [List] \(清單\) 檢視的 [Groups] \(群組\) 類別會顯示所有群組，以及指派給每個群組的角色。 在我們的範例中，群組清單是透過同步處理，從叢集 [Domain] \(網域\) 設定之 [Access user group] \(存取使用者群組\)  屬性中指定的 Azure AD 群組取得。 請參閱[建立啟用 ESP 的 HDInsight 叢集](./domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)。

    ![Apache Ambari 角色清單視圖群組](./media/hdinsight-authorize-users-to-ambari/roles-list-view-groups.png)

    在上圖中，指派給 "hiveusers" 群組的角色是 [Cluster User] \(叢集使用者\)  。 這是一個唯讀角色，可讓該群組的使用者檢視服務組態和叢集計量，但無法進行變更。

## <a name="log-in-to-ambari-as-a-view-only-user"></a>以僅具有檢視權限的使用者身分登入 Ambari

我們已經為 Azure AD 網域使用者 "hiveuser1" 指派對 Hive 和 Tez 檢視的權限。 當我們啟動 Ambari Web UI 並輸入此使用者的網域認證 (電子郵件格式的 Azure AD 使用者名稱，以及密碼) 時，系統會將此使用者重新導向到 Ambari [Views] \(檢視\) 頁面。 從此頁面中，使用者可以選取任何可存取的檢視。 使用者無法瀏覽網站的任何其他部分，包括儀表板、服務、主機、警示或系統管理員頁面。

![僅含 views 的 Apache Ambari 使用者](./media/hdinsight-authorize-users-to-ambari/ambari-user-views-only.png)

## <a name="log-in-to-ambari-as-a-cluster-user"></a>以叢集使用者身分登入 Ambari

我們已經將 Azure AD 網域使用者 "hiveuser2" 指派給 [Cluster User] \(叢集使用者\)  角色。 此角色能夠存取儀表板及所有功能表項目。 叢集使用者獲允許使用的選項比系統管理員少。 例如，hiveuser2 可以檢視每個服務的組態，但無法加以編輯。

![Apache Ambari 儀表板顯示](./media/hdinsight-authorize-users-to-ambari/user-cluster-user-role.png)

## <a name="next-steps"></a>下一步

* [在有 ESP 的 HDInsight 中設定 Apache Hive 原則](./domain-joined/apache-domain-joined-run-hive.md)
* [管理 ESP HDInsight 叢集](./domain-joined/apache-domain-joined-manage.md)
* [在 HDInsight 中搭配 Apache Hadoop 使用 Apache Hive 檢視](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [將 Azure AD 使用者同步至叢集](hdinsight-sync-aad-users-to-cluster.md)
* [使用 Apache Ambari REST API 來管理 HDInsight 叢集](./hdinsight-hadoop-manage-ambari-rest-api.md)
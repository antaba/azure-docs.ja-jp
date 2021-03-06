---
title: "Azure Stack でのオファーのサブスクライブ | Microsoft Docs"
description: "Azure Stack でオファーのサブスクリプションを作成する"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 7f3f8683-ef09-4838-92ed-41f2fddbbbed
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/06/2018
ms.author: brenduns
ms.openlocfilehash: 9b35b497c264fab2b8352eda82fe6d4e1fc274e9
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2018
---
# <a name="create-subscriptions-to-offers-in-azure-stack"></a>Azure Stack でオファーのサブスクリプションを作成する

*適用先: Azure Stack 統合システムと Azure Stack Development Kit*

[オファーを作成](azure-stack-create-offer.md)した後、ユーザーは、それを使用できるようにするために、そのオファーに対するサブスクリプションが必要になります。   
- クラウド オペレーターの場合は、管理者ポータル内からユーザー向けにサブスクリプションを作成できます。  作成したサブスクリプションは、パブリック オファーとプライベート オファーの両方に対応します。
- テナント ユーザーの場合は、ユーザー ポータルを使用するときにパブリック オファーにサブスクライブできます。  

以下のセクションでは、ユーザーのサブスクリプションを作成する際のクラウド オペレーター向けのガイダンスと、ユーザーとしてオファーをサブスクライブする方法を示します。

## <a name="create-a-subscription-as-a-cloud-operator"></a>クラウド オペレーターとしてサブスクリプションを作成する
クラウド オペレーターは、管理ポータルを使用して、ユーザー向けのオファーのサブスクリプションを作成できます。  サブスクリプションは、所有するディレクトリ テナントのメンバーに対して作成できます。  [マルチテナント](azure-stack-enable-multitenancy.md)が有効になっている場合は、追加のディレクトリ テナント内のユーザーに対してサブスクリプションを作成することもできます。

パブリック オファーとプライベート　オファー両方のサブスクリプションを作成できます。  テナントが独自のサブスクリプションを作成しないようにする場合は、すべてのオファーをプライベートにしてから、テナントの代わりにサブスクリプションを作成します。 この手法は、Azure Stack を外部の請求システムまたはサービス カタログ システムと統合する際に一般的です。

ユーザーのサブスクリプションを作成すると、そのユーザーはユーザー ポータルにログインすることができ、オファーをサブスクライブしていることがわかります。  

### <a name="to-create-the-subscription-for-a-user"></a>ユーザーのサブスクリプションを作成するには
1.  管理ポータルで、**[ユーザー サブスクリプション]** に移動します。
2.  **[追加]** を選択して、**[新しいユーザー サブスクリプション]** ウィンドウを開きます。 ここで、次の詳細を指定します。  

  a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 **[表示名]** - "*ユーザー サブスクリプション名*" として表示される、サブスクリプションを識別するための表示名。

  b. **[ユーザー]** - このサブスクリプションで使用可能なディレクトリ テナントからユーザーを指定します。 このユーザー名は "*所有者*" として表示されます。  ユーザー名の形式は、ID ソリューションによって異なります。 例:    

     -  **Azure AD:** *&lt;user1>@&lt;contoso.onmicrosoft.com>*

     -   **AD FS:** *&lt;user1>@&lt;azurestack.local>*     

  c.    **[ディレクトリ テナント]** - ユーザー アカウントが属しているディレクトリ テナントを選択します。 マルチテナントを有効にしていない場合は、ローカルのディレクトリ テナントのみが使用可能です。

3.  **[Offer]\(オファー\)** を選択して **[Offers]\(オファー\)** ウィンドウを開き、このサブスクリプションの**オファー**を選択します。 ユーザーのサブスクリプションを作成しているため、プライベート オファーまたはパブリック オファーを選択できます。

4.  **[作成]** を選択してサブスクリプションを作成します。 これで、**[ユーザー サブスクリプション]** ウィンドウに新しいサブスクリプションが表示されます。  後で、ユーザーがユーザー ポータルにログインすると、このサブスクリプションの詳細を確認できます。

### <a name="to-make-an-add-on-plan-available"></a>アドオン プランを利用できるようにするには  
クラウド オペレーターは、以前に作成したサブスクリプションにいつでもアドオン プランを追加できます。   
1.  管理ポータルで、**[その他のサービス]** > **[ユーザー サブスクリプション]** の順に移動し、変更するサブスクリプションを選択します。   

2.  **[アドオン]** > **[追加]** の順に選択して、**[プランの追加]** ウィンドウを開きます。  

3.  アドオンとして追加するプランを選択します。  その後、コンソールには、サブスクリプションに関連付けられたプランが表示されます。




## <a name="create-a-subscription-as-a-user"></a>ユーザーとしてサブスクリプションを作成する
ユーザーの場合は、ユーザー ポータルにサインインして、ディレクトリ テナント (組織) からパブリック オファーやアドオン プランを探してサブスクライブすることができます。 Azure Stack 環境で[マルチテナント](azure-stack-enable-multitenancy.md)がサポートされている場合は、リモートのディレクトリ テナントからオファーをサブスクライブすることができます。

### <a name="to-subscribe-to-an-offer"></a>オファーをサブスクライブするには
1. ユーザーとして Azure Stack のユーザー ポータル (https://portal.local.azurestack.external) に[サインイン](azure-stack-connect-azure-stack.md)し、**[サブスクリプションの取得]** をクリックします。

   ![サブスクリプションの取得](media/azure-stack-subscribe-plan-provision-vm/image01.png)
2. **[表示名]** フィールドに、サブスクリプションの名前を入力し、**[Offer]\(オファー\)** をクリックします。**[Choose an offer]\(オファーの選択\)** ウィンドウでいずれかのオファーをクリックし、**[作成]** をクリックします。

   ![オファーの作成](media/azure-stack-subscribe-plan-provision-vm/image02.png)
3. 作成したサブスクリプションを表示するには、**[その他のサービス]**、**[サブスクリプション]** の順にクリックし、作成した新しいサブスクリプションをクリックします。  

オファーをサブスクライブしたら、ポータルを更新して、どのサービスが新しいサブスクリプションの一部であるかを確認します。

### <a name="to-subscribe-to-an-add-on-plan"></a>アドオン プランをサブスクライブするには
オファーにアドオン プランがある場合は、そのプランをいつでもサブスクリプションに追加できます。  

1. ユーザー ポータルで、**[その他のサービス]** > **[サブスクリプション]** の順に選択し、変更するサブスクリプションを選択します。

2. **[プランの追加]** ボタンを選択し、必要なアドオン プランを選択します。 **[プランの追加]** を使用できない場合は、そのサブスクリプションに関連付けられているオファーのアドオン プランがありません。



## <a name="next-steps"></a>次の手順
[仮想マシンのプロビジョニング](azure-stack-provision-vm.md)

---
title: CAF:シンプルなワークロード向けガバナンス設計
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
description: Azure ガバナンス管理を構成してユーザーがシンプルなワークロードをデプロイできるようにするためのガイダンス
author: petertaylor9999
ms.date: 02/11/2019
ms.openlocfilehash: 6bf2f15f706140955df29f7f372068c80ce1ad21
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59640585"
---
# <a name="governance-design-for-a-simple-workload"></a><span data-ttu-id="53482-103">シンプルなワークロード向けガバナンス設計</span><span class="sxs-lookup"><span data-stu-id="53482-103">Governance design for a simple workload</span></span>

<span data-ttu-id="53482-104">このガイドの目的は、単一チームおよびシンプルなワークロードをサポートするための、Azure におけるリソース ガバナンス モデルの設計プロセスについて説明することです。</span><span class="sxs-lookup"><span data-stu-id="53482-104">The goal of this guidance is to help you learn the process for designing a resource governance model in Azure to support a single team and a simple workload.</span></span> <span data-ttu-id="53482-105">一連のガバナンス要件を例として挙げて、これらの要件に対応する実装サンプルをいくつか紹介します。</span><span class="sxs-lookup"><span data-stu-id="53482-105">You'll look at a set of hypothetical governance requirements, then go through several example implementations that satisfy those requirements.</span></span>

<span data-ttu-id="53482-106">基本導入ステージの目標は、シンプルなワークロードを Azure にデプロイすることです。</span><span class="sxs-lookup"><span data-stu-id="53482-106">In the foundational adoption stage, our goal is to deploy a simple workload to Azure.</span></span> <span data-ttu-id="53482-107">これには、次の要件が伴います。</span><span class="sxs-lookup"><span data-stu-id="53482-107">This results in the following requirements:</span></span>

* <span data-ttu-id="53482-108">シンプルなワークロードのデプロイと保守を担当する 1 人の**ワークロード所有者**の ID を管理する。</span><span class="sxs-lookup"><span data-stu-id="53482-108">Identity management for a single **workload owner** who is responsible for deploying and maintaining the simple workload.</span></span> <span data-ttu-id="53482-109">ワークロード所有者には、リソースの作成、読み取り、更新、および削除のアクセス許可、および ID 管理システムで他のユーザーにこれらの特権を委任するアクセス許可が必要です。</span><span class="sxs-lookup"><span data-stu-id="53482-109">The workload owner requires permission to create, read, update, and delete resources as well as permission to delegate these rights to other users in the identity management system.</span></span>
* <span data-ttu-id="53482-110">シンプルなワークロードのすべてのリソースを 1 つの管理単位として管理する。</span><span class="sxs-lookup"><span data-stu-id="53482-110">Manage all resources for the simple workload as a single management unit.</span></span>

## <a name="licensing-azure"></a><span data-ttu-id="53482-111">Azure のライセンス</span><span class="sxs-lookup"><span data-stu-id="53482-111">Licensing Azure</span></span>

<span data-ttu-id="53482-112">ガバナンス モデルの設計を開始する前に、Azure のライセンスの取得方法を理解することが重要です。</span><span class="sxs-lookup"><span data-stu-id="53482-112">Before you begin designing our governance model, it's important to understand how Azure is licensed.</span></span> <span data-ttu-id="53482-113">ご自身の Azure ライセンスに関連付けられている管理者アカウントには、ご使用の Azure リソースすべてに対する最高レベルのアクセス権が付与されているためです。</span><span class="sxs-lookup"><span data-stu-id="53482-113">This is because the administrative accounts associated with your Azure license have the highest level of access to all of your Azure resources.</span></span> <span data-ttu-id="53482-114">これらの管理者アカウントにより、ご自身のガバナンス モデルの基礎が形成されます。</span><span class="sxs-lookup"><span data-stu-id="53482-114">These administrative accounts form the basis of your governance model.</span></span>  

> [!NOTE]
> <span data-ttu-id="53482-115">お客様の組織の既存の[マイクロソフトエンタープライズ契約](https://www.microsoft.com/licensing/licensing-programs/enterprise.aspx)に Azure が含まれていない場合は、前払いによる年額コミットメントを行うことで Azure を追加できます。</span><span class="sxs-lookup"><span data-stu-id="53482-115">If your organization has an existing [Microsoft Enterprise Agreement](https://www.microsoft.com/licensing/licensing-programs/enterprise.aspx) that does not include Azure, Azure can be added by making an upfront monetary commitment.</span></span> <span data-ttu-id="53482-116">詳細については、「[Azure のエンタープライズ向けライセンス](https://azure.microsoft.com/pricing/enterprise-agreement/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="53482-116">See [licensing Azure for the enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/) for more information.</span></span>

<span data-ttu-id="53482-117">Azure がご自身の組織のエンタープライズ契約に追加されたとき、組織は **Azure アカウント**を作成するよう求められました。</span><span class="sxs-lookup"><span data-stu-id="53482-117">When Azure was added to your organization's Enterprise Agreement, your organization was prompted to create an **Azure account**.</span></span> <span data-ttu-id="53482-118">アカウントの作成プロセス中に、**Azure アカウント所有者**が、Azure Active Directory (Azure AD) テナントおよび**グローバル管理者**アカウントと共に作成されています。</span><span class="sxs-lookup"><span data-stu-id="53482-118">During the account creation process, an **Azure account owner** was created, as well as an Azure Active Directory (Azure AD) tenant with a **global administrator** account.</span></span> <span data-ttu-id="53482-119">Azure AD テナントとは、Azure AD の安全な専用インスタンスを表す論理コンストラクトです。</span><span class="sxs-lookup"><span data-stu-id="53482-119">An Azure AD tenant is a logical construct that represents a secure, dedicated instance of Azure AD.</span></span>

<span data-ttu-id="53482-120">![Azure アカウント マネージャーと Azure AD グローバル管理者が含まれる Azure アカウント](../../_images/governance-3-0.png)
*図 1. Azure アカウント マネージャーと Azure AD グローバル管理者が含まれる Azure アカウント。*</span><span class="sxs-lookup"><span data-stu-id="53482-120">![Azure account with Azure Account Manager and Azure AD global administrator](../../_images/governance-3-0.png)
*Figure 1. An Azure account with an Account Manager and Azure AD Global Administrator.*</span></span>

## <a name="identity-management"></a><span data-ttu-id="53482-121">ID 管理</span><span class="sxs-lookup"><span data-stu-id="53482-121">Identity management</span></span>

<span data-ttu-id="53482-122">Azure は、[Azure AD](/azure/active-directory) のみを信頼してユーザーを認証し、リソースへのユーザー アクセスを承認します。したがって、Azure AD は ID 管理システムです。</span><span class="sxs-lookup"><span data-stu-id="53482-122">Azure only trusts [Azure AD](/azure/active-directory) to authenticate users and authorize user access to resources, so Azure AD is our identity management system.</span></span> <span data-ttu-id="53482-123">Azure AD グローバル管理者には最高レベルのアクセス許可が付与されており、ユーザーの作成やアクセス許可の割り当てなど、ID に関連するすべてのアクションを実行できます。</span><span class="sxs-lookup"><span data-stu-id="53482-123">The Azure AD global administrator has the highest level of permissions and can perform all actions related to identity, including creating users and assigning permissions.</span></span>

<span data-ttu-id="53482-124">ここでの要件は、シンプルなワークロードのデプロイと保守を担当する 1 人の**ワークロード所有者**の ID を管理することです。</span><span class="sxs-lookup"><span data-stu-id="53482-124">Our requirement is identity management for a single **workload owner** who is responsible for deploying and maintaining the simple workload.</span></span> <span data-ttu-id="53482-125">ワークロード所有者には、リソースの作成、読み取り、更新、および削除のアクセス許可、および ID 管理システムで他のユーザーにこれらの特権を委任するアクセス許可が必要です。</span><span class="sxs-lookup"><span data-stu-id="53482-125">The workload owner requires permission to create, read, update, and delete resources as well as permission to delegate these rights to other users in the identity management system.</span></span>

<span data-ttu-id="53482-126">Azure AD のグローバル管理者が、**ワークロード所有者**を表す**ワークロード所有者**アカウントを作成します。</span><span class="sxs-lookup"><span data-stu-id="53482-126">Our Azure AD global administrator will create the **workload owner** account for the **workload owner**:</span></span>

<span data-ttu-id="53482-127">![Azure AD のグローバル管理者がワークロード所有者アカウントを作成する](../../_images/governance-1-2.png)
*図 2. Azure AD のグローバル管理者がワークロード所有者のユーザー アカウントを作成する。*</span><span class="sxs-lookup"><span data-stu-id="53482-127">![The Azure AD global administrator creates the workload owner account](../../_images/governance-1-2.png)
*Figure 2. The Azure AD global administrator creates the workload owner user account.*</span></span>

<span data-ttu-id="53482-128">このユーザーが**サブスクリプション**に追加されるまで、リソースのアクセス許可を割り当てることができません。そこで、次の 2 つのセクションでは、これを行います。</span><span class="sxs-lookup"><span data-stu-id="53482-128">You aren't able to assign resource access permission until this user is added to a **subscription**, so you'll do that in the next two sections.</span></span>

## <a name="resource-management-scope"></a><span data-ttu-id="53482-129">リソース管理のスコープ</span><span class="sxs-lookup"><span data-stu-id="53482-129">Resource management scope</span></span>

<span data-ttu-id="53482-130">組織によってデプロイされるリソースの数が増えると、それらのリソースを管理する複雑さも増します。</span><span class="sxs-lookup"><span data-stu-id="53482-130">As the number of resources deployed by your organization grows, the complexity of governing those resources grows as well.</span></span> <span data-ttu-id="53482-131">Azure では論理コンテナー階層が実装されており、これにより、組織がさまざまな細分性レベルのグループ (**スコープ**) でリソースを管理できます。</span><span class="sxs-lookup"><span data-stu-id="53482-131">Azure implements a logical container hierarchy to enable your organization to manage your resources in groups at various levels of granularity, also known as **scope**.</span></span>

<span data-ttu-id="53482-132">最上位レベルのリソース管理のスコープは**サブスクリプション** レベルです。</span><span class="sxs-lookup"><span data-stu-id="53482-132">The top level of resource management scope is the **subscription** level.</span></span> <span data-ttu-id="53482-133">サブスクリプションは、Azure **アカウント所有者**によって作成されます。このユーザーは財務コミットメントを確立し、そのサブスクリプションに関連付けられているすべての Azure リソースに対する支払いを行う責任があります。</span><span class="sxs-lookup"><span data-stu-id="53482-133">A subscription is created by the Azure **account owner**, who establishes the financial commitment and is responsible for paying for all Azure resources associated with the subscription:</span></span>

<span data-ttu-id="53482-134">![Azure アカウント所有者がサブスクリプションを作成する](../../_images/governance-1-3.png)
*図 3. Azure アカウント所有者がサブスクリプションを作成する。*</span><span class="sxs-lookup"><span data-stu-id="53482-134">![The Azure account owner creates a subscription](../../_images/governance-1-3.png)
*Figure 3. The Azure account owner creates a subscription.*</span></span>

<span data-ttu-id="53482-135">サブスクリプションが作成されると、Azure **アカウント所有者**は、Azure AD テナントをサブスクリプションに関連付けます。この Azure AD テナントは、ユーザーの認証と承認に使用されます。</span><span class="sxs-lookup"><span data-stu-id="53482-135">When the subscription is created, the Azure **account owner** associates an Azure AD tenant with the subscription, and this Azure AD tenant is used for authenticating and authorizing users:</span></span>

<span data-ttu-id="53482-136">![Azure アカウント所有者が Azure AD テナントをサブスクリプションに関連付ける](../../_images/governance-1-4.png)
*図 4. Azure アカウント所有者が Azure AD テナントをサブスクリプションに関連付ける。*</span><span class="sxs-lookup"><span data-stu-id="53482-136">![The Azure account owner associates the Azure AD tenant with the subscription](../../_images/governance-1-4.png)
*Figure 4. The Azure account owner associates the Azure AD tenant with the subscription.*</span></span>

<span data-ttu-id="53482-137">お気付きかもしれませんが、現在サブスクリプションにはユーザーが関連付けられていません。つまり、リソースを管理するアクセス許可は、誰にも付与されていません。</span><span class="sxs-lookup"><span data-stu-id="53482-137">You may have noticed that there is currently no user associated with the subscription, which means that no one has permission to manage resources.</span></span> <span data-ttu-id="53482-138">実際には、**アカウント所有者**はサブスクリプションの所有者であり、サブスクリプションのリソースに対して任意のアクションを実行するアクセス許可を持っています。</span><span class="sxs-lookup"><span data-stu-id="53482-138">In reality, the **account owner** is the owner of the subscription and has permission to take any action on a resource in the subscription.</span></span> <span data-ttu-id="53482-139">しかし、実のところ、**アカウント所有者**は、組織の財務担当者である可能性が高く、リソースの作成、読み取り、更新、および削除を担当しません。これらのタスクは**ワークロード所有者**によって実行されます。</span><span class="sxs-lookup"><span data-stu-id="53482-139">However, in practical terms the **account owner** is more than likely a finance person in your organization and is not responsible for creating, reading, updating, and deleting resources - those tasks will be performed by the **workload owner**.</span></span> <span data-ttu-id="53482-140">したがって、**ワークロード所有者**をサブスクリプションに追加して、アクセス許可を割り当てる必要があります。</span><span class="sxs-lookup"><span data-stu-id="53482-140">Therefore, you need to add the **workload owner** to the subscription and assign permissions.</span></span>

<span data-ttu-id="53482-141">**アカウント所有者**は、現時点で**ワークロード所有者**をサブスクリプションに追加するアクセス許可を持つ唯一のユーザーです。したがって、アカウント所有者が、**ワークロード所有者**をサブスクリプションに追加します。</span><span class="sxs-lookup"><span data-stu-id="53482-141">Since the **account owner** is currently the only user with permission to add the **workload owner** to the subscription, they add the **workload owner** to the subscription:</span></span>

<span data-ttu-id="53482-142">![Azure アカウント所有者が**ワークロード所有者**をサブスクリプションに追加する](../../_images/governance-1-5.png)
*図 5. Azure アカウント所有者がワークロード所有者をサブスクリプションに追加する。*</span><span class="sxs-lookup"><span data-stu-id="53482-142">![The Azure account owner adds the **workload owner** to the subscription](../../_images/governance-1-5.png)
*Figure 5. The Azure account owner adds the workload owner to the subscription.*</span></span>

<span data-ttu-id="53482-143">Azure **アカウント所有者**は、[ロールベースのアクセス制御 (RBAC)](/azure/role-based-access-control/) ロールを割り当てることで、**ワークロード所有者**にアクセス許可を付与します。</span><span class="sxs-lookup"><span data-stu-id="53482-143">The Azure **account owner** grants permissions to the **workload owner** by assigning a [role-based access control (RBAC)](/azure/role-based-access-control/) role.</span></span> <span data-ttu-id="53482-144">RBAC ロールにより、個々のリソースの種類または一連のリソースの種類に対して**ワークロード所有者**が持つ一連のアクセス許可が指定されます。</span><span class="sxs-lookup"><span data-stu-id="53482-144">The RBAC role specifies a set of permissions that the **workload owner** has for an individual resource type or a set of resource types.</span></span>

<span data-ttu-id="53482-145">この例では、**アカウント所有者**は、[組み込みの**所有者**ロール](/azure/role-based-access-control/built-in-roles#owner)を割り当てました。</span><span class="sxs-lookup"><span data-stu-id="53482-145">Notice that in this example, the **account owner** has assigned the [built-in **owner** role](/azure/role-based-access-control/built-in-roles#owner):</span></span>

<span data-ttu-id="53482-146">![**ワークロード所有者**に組み込みの所有者ロールが割り当てられる](../../_images/governance-1-6.png)
*図 6. **ワークロード所有者**に組み込みの所有者ロールが割り当てられる。*</span><span class="sxs-lookup"><span data-stu-id="53482-146">![The **workload owner** was assigned the built-in owner role](../../_images/governance-1-6.png)
*Figure 6. The workload owner was assigned the built-in owner role.*</span></span>

<span data-ttu-id="53482-147">組み込みの**所有者**ロールによって、サブスクリプション スコープで、すべてのアクセス許可が**ワークロード所有者**に付与されます。</span><span class="sxs-lookup"><span data-stu-id="53482-147">The built-in **owner** role grants all permissions to the **workload owner** at the subscription scope.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="53482-148">Azure **アカウント所有者**は、サブスクリプションに関連付けられている財務コミットメントの責任を負いますが、**ワークロード所有者**にも同じアクセス許可が付与されています。</span><span class="sxs-lookup"><span data-stu-id="53482-148">The Azure **account owner** is responsible for the financial commitment associated with the subscription, but the **workload owner** has the same permissions.</span></span> <span data-ttu-id="53482-149">**アカウント所有者**は、**ワークロード所有者**がサブスクリプション予算内でリソースをデプロイしていることを信頼する必要があります。</span><span class="sxs-lookup"><span data-stu-id="53482-149">The **account owner** must trust the **workload owner** to deploy resources that are within the subscription budget.</span></span>

<span data-ttu-id="53482-150">次の管理スコープ レベルは**リソース グループ** レベルです。</span><span class="sxs-lookup"><span data-stu-id="53482-150">The next level of management scope is the **resource group** level.</span></span> <span data-ttu-id="53482-151">リソース グループは、リソースの論理コンテナーです。</span><span class="sxs-lookup"><span data-stu-id="53482-151">A resource group is a logical container for resources.</span></span> <span data-ttu-id="53482-152">リソース グループ レベルで適用された操作は、グループ内のすべてのリソースに適用されます。</span><span class="sxs-lookup"><span data-stu-id="53482-152">Operations applied at the resource group level apply to all resources in a group.</span></span> <span data-ttu-id="53482-153">さらに、各ユーザーのアクセス許可は、そのスコープで明示的に変更された場合を除き、上のレベルから継承されることにも注意してください。</span><span class="sxs-lookup"><span data-stu-id="53482-153">Also, it's important to note that permissions for each user are inherited from the next level up unless they are explicitly changed at that scope.</span></span>

<span data-ttu-id="53482-154">これを確認するために、**ワークロード所有者**がリソース グループを作成するとき、何が起こるかを見てみましょう。</span><span class="sxs-lookup"><span data-stu-id="53482-154">To illustrate this, let's look at what happens when the **workload owner** creates a resource group:</span></span>

<span data-ttu-id="53482-155">![**ワークロード所有者**がリソース グループを作成する](../../_images/governance-1-7.png)
*図 7. ワークロード所有者がリソース グループを作成し、リソース グループ スコープで組み込みの所有者ロールを継承する。*</span><span class="sxs-lookup"><span data-stu-id="53482-155">![The **workload owner** creates a resource group](../../_images/governance-1-7.png)
*Figure 7. The workload owner creates a resource group and inherits the built-in owner role at the resource group scope.*</span></span>

<span data-ttu-id="53482-156">この場合も、組み込みの**所有者**ロールによって、リソース グループ スコープで、すべてのアクセス許可が**ワークロード所有者**に付与されます。</span><span class="sxs-lookup"><span data-stu-id="53482-156">Again, the built-in **owner** role grants all permissions to the **workload owner** at the resource group scope.</span></span> <span data-ttu-id="53482-157">先ほど説明したように、このロールはサブスクリプション レベルから継承されます。</span><span class="sxs-lookup"><span data-stu-id="53482-157">As discussed earlier, this role is inherited from the subscription level.</span></span> <span data-ttu-id="53482-158">このスコープで別のロールがこのユーザーに割り当てられると、そのロールはこのスコープにのみ適用されます。</span><span class="sxs-lookup"><span data-stu-id="53482-158">If a different role is assigned to this user at this scope, it applies to this scope only.</span></span>

<span data-ttu-id="53482-159">最下位レベルの管理スコープは**リソース** レベルです。</span><span class="sxs-lookup"><span data-stu-id="53482-159">The lowest level of management scope is at the **resource** level.</span></span> <span data-ttu-id="53482-160">リソース レベルで適用された操作は、リソース自体にのみ適用されます。</span><span class="sxs-lookup"><span data-stu-id="53482-160">Operations applied at the resource level apply only to the resource itself.</span></span> <span data-ttu-id="53482-161">また、前と同様に、リソース レベルのアクセス許可は、リソース グループ スコープから継承されます。</span><span class="sxs-lookup"><span data-stu-id="53482-161">And once again, permissions at the resource level are inherited from resource group scope.</span></span> <span data-ttu-id="53482-162">たとえば、**ワークロード所有者**が[仮想ネットワーク](/azure/virtual-network/virtual-networks-overview)をリソース グループにデプロイするとどうなるかを見てみましょう。</span><span class="sxs-lookup"><span data-stu-id="53482-162">For example, let's look at what happens if the **workload owner** deploys a [virtual network](/azure/virtual-network/virtual-networks-overview) into the resource group:</span></span>

<span data-ttu-id="53482-163">![**ワークロード所有者**がリソースを作成する](../../_images/governance-1-8.png)
*図 8. ワークロード所有者がリソースを作成し、リソース スコープで組み込みの所有者ロールを継承する。*</span><span class="sxs-lookup"><span data-stu-id="53482-163">![The **workload owner** creates a resource](../../_images/governance-1-8.png)
*Figure 8. The workload owner creates a resource and inherits the built-in owner role at the resource scope.*</span></span>

<span data-ttu-id="53482-164">**ワークロード所有者**は、リソース スコープで所有者ロールを継承します。つまり、ワークロード所有者には、仮想ネットワークに対するすべてのアクセス許可が付与されます。</span><span class="sxs-lookup"><span data-stu-id="53482-164">The **workload owner** inherits the owner role at the resource scope, which means the workload owner has all permissions for the virtual network.</span></span>

## <a name="implementing-the-basic-resource-access-management-model"></a><span data-ttu-id="53482-165">基本的なリソース アクセス管理モデルの実装</span><span class="sxs-lookup"><span data-stu-id="53482-165">Implementing the basic resource access management model</span></span>

<span data-ttu-id="53482-166">次は、前に設計したガバナンス モデルを実装する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="53482-166">Let's move on to learn how to implement the governance model designed earlier.</span></span>

<span data-ttu-id="53482-167">まず、お客様の組織には Azure アカウントが必要です。</span><span class="sxs-lookup"><span data-stu-id="53482-167">To begin, your organization requires an Azure account.</span></span> <span data-ttu-id="53482-168">お客様の組織の既存の[マイクロソフトエンタープライズ契約](https://www.microsoft.com/licensing/licensing-programs/enterprise.aspx)に Azure が含まれていない場合は、前払いによる年額コミットメントを行うことで Azure を追加できます。</span><span class="sxs-lookup"><span data-stu-id="53482-168">If your organization has an existing [Microsoft Enterprise Agreement](https://www.microsoft.com/licensing/licensing-programs/enterprise.aspx) that does not include Azure, Azure can be added by making an upfront monetary commitment.</span></span> <span data-ttu-id="53482-169">詳細については、「[Azure のエンタープライズ向けライセンス](https://azure.microsoft.com/pricing/enterprise-agreement/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="53482-169">See [licensing Azure for the enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/) for more information.</span></span>

<span data-ttu-id="53482-170">Azure アカウントが作成されたら、組織の 1 人を Azure **アカウント所有者**に指定します。</span><span class="sxs-lookup"><span data-stu-id="53482-170">When your Azure account is created, you specify a person in your organization to be the Azure **account owner**.</span></span> <span data-ttu-id="53482-171">Azure Active Directory (Azure AD) テナントは、そのとき既定で作成されます。</span><span class="sxs-lookup"><span data-stu-id="53482-171">An Azure Active Directory (Azure AD) tenant is then created by default.</span></span> <span data-ttu-id="53482-172">Azure **アカウント所有者**は、**ワークロード所有者**となる組織内ユーザーの[ユーザー アカウントを作成する](/azure/active-directory/add-users-azure-active-directory)必要があります。</span><span class="sxs-lookup"><span data-stu-id="53482-172">Your Azure **account owner** must [create the user account](/azure/active-directory/add-users-azure-active-directory) for the person in your organization who is the **workload owner**.</span></span>

<span data-ttu-id="53482-173">次に、Azure **アカウント所有者**は、[サブスクリプションを作成](/partner-center/create-a-new-subscription)し、これに [Azure AD テナントを関連付ける](/azure/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory)必要があります。</span><span class="sxs-lookup"><span data-stu-id="53482-173">Next, your Azure **account owner** must [create a subscription](/partner-center/create-a-new-subscription) and [associate the Azure AD tenant](/azure/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory) with it.</span></span>

<span data-ttu-id="53482-174">サブスクリプションを作成し、これに Azure AD テナントを関連付けたので、最後に、[**ワークロード所有者**を、組み込みの**所有者**ロールを持つサブスクリプションに追加](/azure/billing/billing-add-change-azure-subscription-administrator#add-an-rbac-owner-for-a-subscription-in-azure-portal)します。</span><span class="sxs-lookup"><span data-stu-id="53482-174">Finally, now that the subscription is created and your Azure AD tenant is associated with it, you can [add the **workload owner** to the subscription with the built-in **owner** role](/azure/billing/billing-add-change-azure-subscription-administrator#add-an-rbac-owner-for-a-subscription-in-azure-portal).</span></span>

## <a name="next-steps"></a><span data-ttu-id="53482-175">次の手順</span><span class="sxs-lookup"><span data-stu-id="53482-175">Next steps</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="53482-176">基本的なワークロードを Azure にデプロイする</span><span class="sxs-lookup"><span data-stu-id="53482-176">Deploy a basic workload to Azure</span></span>](../../infrastructure/basic-workload.md)
> [!div class="nextstepaction"]
> [<span data-ttu-id="53482-177">複数チーム向けリソース アクセスについて確認する</span><span class="sxs-lookup"><span data-stu-id="53482-177">Learn about resource access for multiple teams</span></span>](governance-multiple-teams.md)

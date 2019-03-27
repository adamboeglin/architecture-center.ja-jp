---
title: Azure Resource Manager テンプレートのリソースを更新する
description: Azure Resource Manager テンプレートの機能を拡張して、リソースを更新する方法について説明します。
author: petertay
ms.date: 10/31/2018
ms.topic: article
ms.service: architecture-center
ms.subservice: reference-architecture
ms.openlocfilehash: de76e69e94917bbbe94c0f87fda2cdbe415181dc
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58241713"
---
# <a name="update-a-resource-in-an-azure-resource-manager-template"></a><span data-ttu-id="f979a-103">Azure Resource Manager テンプレートのリソースを更新する</span><span class="sxs-lookup"><span data-stu-id="f979a-103">Update a resource in an Azure Resource Manager template</span></span>

<span data-ttu-id="f979a-104">デプロイ中にリソースの更新が必要になるシナリオはいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="f979a-104">There are some scenarios in which you need to update a resource during a deployment.</span></span> <span data-ttu-id="f979a-105">このシナリオは、他の依存リソースが作成されるまで、リソースの一部のプロパティを指定できないときに発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f979a-105">You might encounter this scenario when you cannot specify all the properties for a resource until other, dependent resources are created.</span></span> <span data-ttu-id="f979a-106">たとえば、ロード バランサーのバックエンド プールを作成する場合、仮想マシン (VM) のネットワーク インターフェイス (NIC) をバックエンド プールに含めるために、その NIC を更新することがあります。</span><span class="sxs-lookup"><span data-stu-id="f979a-106">For example, if you create a backend pool for a load balancer, you might update the network interfaces (NICs) on your virtual machines (VMs) to include them in the backend pool.</span></span> <span data-ttu-id="f979a-107">そして、Resource Manager でデプロイ中のリソース更新がサポートされている間、エラーが発生しないように、また、デプロイが更新として確実に処理されるようにするには、テンプレートを適切に設計する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f979a-107">And while Resource Manager supports updating resources during deployment, you must design your template correctly to avoid errors and to ensure the deployment is handled as an update.</span></span>

<span data-ttu-id="f979a-108">最初に、テンプレートでそのリソースを 1 回参照してリソースを作成し、その後同じ名前でそのリソースを参照し、後でそのリソースを更新します。</span><span class="sxs-lookup"><span data-stu-id="f979a-108">First, you must reference the resource once in the template to create it and then reference the resource by the same name to update it later.</span></span> <span data-ttu-id="f979a-109">しかし、テンプレート内で同じ名前のリソースが 2 つあると、Resource Manager は例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="f979a-109">However, if two resources have the same name in a template, Resource Manager throws an exception.</span></span> <span data-ttu-id="f979a-110">このエラーが発生しないようにするには、更新されたリソースを、サブテンプレートとしてリンクまたは挿入されている 2 番目のテンプレートで、`Microsoft.Resources/deployments` リソース タイプを使用して指定します。</span><span class="sxs-lookup"><span data-stu-id="f979a-110">To avoid this error, specify the updated resource in a second template that's either linked or included as a subtemplate using the `Microsoft.Resources/deployments` resource type.</span></span>

<span data-ttu-id="f979a-111">次に、入れ子になっているテンプレートに、変更する既存のプロパティの名前を指定するか、追加するプロパティの新しい名前を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f979a-111">Second, you must either specify the name of the existing property to change or a new name for a property to add in the nested template.</span></span> <span data-ttu-id="f979a-112">元のプロパティと、そのプロパティの元の値も指定しなければなりません。</span><span class="sxs-lookup"><span data-stu-id="f979a-112">You must also specify the original properties and their original values.</span></span> <span data-ttu-id="f979a-113">元のプロパティと値を指定しないと、新しいリソースを作成する必要があるものと Resource Manager からみなされ、元のリソースは削除されます。</span><span class="sxs-lookup"><span data-stu-id="f979a-113">If you fail to provide the original properties and values, Resource Manager assumes you want to create a new resource and deletes the original resource.</span></span>

## <a name="example-template"></a><span data-ttu-id="f979a-114">テンプレートの例</span><span class="sxs-lookup"><span data-stu-id="f979a-114">Example template</span></span>

<span data-ttu-id="f979a-115">これを実証するテンプレートの例を見てみましょう。</span><span class="sxs-lookup"><span data-stu-id="f979a-115">Let's look at an example template that demonstrates this.</span></span> <span data-ttu-id="f979a-116">このテンプレートでは、`firstSubnet` という名前のサブネットを含む `firstVNet` という名前の仮想ネットワークをデプロイします。</span><span class="sxs-lookup"><span data-stu-id="f979a-116">Our template deploys a virtual network named `firstVNet` that has one subnet named `firstSubnet`.</span></span> <span data-ttu-id="f979a-117">そして、`nic1` という名前の仮想ネットワーク インターフェイス (NIC) をデプロイし、サブネットに関連付けます。</span><span class="sxs-lookup"><span data-stu-id="f979a-117">It then deploys a virtual network interface (NIC) named `nic1` and associates it with our subnet.</span></span> <span data-ttu-id="f979a-118">その後、`secondSubnet` という名前の 2 番目のサブネットを追加することにより、`updateVNet` という名前のデプロイ リソースに、`firstVNet` リソースを更新する入れ子になったテンプレートが追加されます。</span><span class="sxs-lookup"><span data-stu-id="f979a-118">Then, a deployment resource named `updateVNet` includes a nested template that updates our `firstVNet` resource by adding a second subnet named `secondSubnet`.</span></span>

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
      {
      "apiVersion": "2016-03-30",
      "name": "firstVNet",
      "location":"[resourceGroup().location]",
      "type": "Microsoft.Network/virtualNetworks",
      "properties": {
          "addressSpace":{"addressPrefixes": [
              "10.0.0.0/22"
          ]},
          "subnets":[
              {
                  "name":"firstSubnet",
                  "properties":{
                    "addressPrefix":"10.0.0.0/24"
                  }
              }
            ]
      }
    },
    {
        "apiVersion": "2015-06-15",
        "type":"Microsoft.Network/networkInterfaces",
        "name":"nic1",
        "location":"[resourceGroup().location]",
        "dependsOn": [
            "firstVNet"
        ],
        "properties": {
            "ipConfigurations":[
                {
                    "name":"ipconfig1",
                    "properties": {
                        "privateIPAllocationMethod":"Dynamic",
                        "subnet": {
                            "id": "[concat(resourceId('Microsoft.Network/virtualNetworks','firstVNet'),'/subnets/firstSubnet')]"
                        }
                    }
                }
            ]
        }
    },
    {
      "apiVersion": "2015-01-01",
      "type": "Microsoft.Resources/deployments",
      "name": "updateVNet",
      "dependsOn": [
          "nic1"
      ],
      "properties": {
        "mode": "Incremental",
        "parameters": {},
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
              {
                  "apiVersion": "2016-03-30",
                  "name": "firstVNet",
                  "location":"[resourceGroup().location]",
                  "type": "Microsoft.Network/virtualNetworks",
                  "properties": {
                      "addressSpace": "[reference('firstVNet').addressSpace]",
                      "subnets":[
                          {
                              "name":"[reference('firstVNet').subnets[0].name]",
                              "properties":{
                                  "addressPrefix":"[reference('firstVNet').subnets[0].properties.addressPrefix]"
                                  }
                          },
                          {
                              "name":"secondSubnet",
                              "properties":{
                                  "addressPrefix":"10.0.1.0/24"
                                  }
                          }
                     ]
                  }
              }
          ],
          "outputs": {}
          }
        }
    }
  ],
  "outputs": {}
}
```

<span data-ttu-id="f979a-119">最初に、`firstVNet` リソースのリソース オブジェクトを見てみましょう。</span><span class="sxs-lookup"><span data-stu-id="f979a-119">Let's take a look at the resource object for our `firstVNet` resource first.</span></span> <span data-ttu-id="f979a-120">入れ子になったテンプレートで `firstVNet` の設定を再度指定しますのでご注意ください。この理由は、Resource Manager では同じテンプレート内での同じデプロイ名を使用することは許可されず、入れ子になったテンプレートは別のテンプレートと見なされるためです。</span><span class="sxs-lookup"><span data-stu-id="f979a-120">Notice that we respecify the settings for our `firstVNet` in a nested template&mdash;this is because Resource Manager doesn't allow the same deployment name within the same template and nested templates are considered to be a different template.</span></span> <span data-ttu-id="f979a-121">`firstSubnet` リソースに値を再度指定することにより、リソース マネージャーは既存のリソースを削除する代わりに更新し、再度デプロイします。</span><span class="sxs-lookup"><span data-stu-id="f979a-121">By respecifying our values for our `firstSubnet` resource, we are telling Resource Manager to update the existing resource instead of deleting it and redeploying it.</span></span> <span data-ttu-id="f979a-122">最後に、`secondSubnet` の新しい設定は、この更新中に選択されます。</span><span class="sxs-lookup"><span data-stu-id="f979a-122">Finally, our new settings for `secondSubnet` are picked up during this update.</span></span>

## <a name="try-the-template"></a><span data-ttu-id="f979a-123">テンプレートを試行する</span><span class="sxs-lookup"><span data-stu-id="f979a-123">Try the template</span></span>

<span data-ttu-id="f979a-124">テンプレートの例は [GitHub][github] で入手できます。</span><span class="sxs-lookup"><span data-stu-id="f979a-124">An example template is available on [GitHub][github].</span></span> <span data-ttu-id="f979a-125">テンプレートをデプロイするには、次の [Azure CLI][cli] コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="f979a-125">To deploy the template, run the following [Azure CLI][cli] commands:</span></span>

```bash
az group create --location <location> --name <resource-group-name>
az group deployment create -g <resource-group-name> \
    --template-uri https://raw.githubusercontent.com/mspnp/template-examples/master/example1-update/deploy.json
```

<span data-ttu-id="f979a-126">デプロイが完了したら、ポータルで指定したリソース グループを開きます。</span><span class="sxs-lookup"><span data-stu-id="f979a-126">Once deployment has finished, open the resource group you specified in the portal.</span></span> <span data-ttu-id="f979a-127">`firstVNet` という名前の仮想ネットワークと、`nic1` という名前の NIC が表示されます。</span><span class="sxs-lookup"><span data-stu-id="f979a-127">You see a virtual network named `firstVNet` and a NIC named `nic1`.</span></span> <span data-ttu-id="f979a-128">`firstVNet`、`subnets` の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="f979a-128">Click `firstVNet`, then click `subnets`.</span></span> <span data-ttu-id="f979a-129">最初に作成された `firstSubnet` のほか、`updateVNet` リソースで追加された `secondSubnet` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="f979a-129">You see the `firstSubnet` that was originally created, and you see the `secondSubnet` that was added in the `updateVNet` resource.</span></span>

![元のサブネットと、更新されたサブネット](../_images/firstVNet-subnets.png)

<span data-ttu-id="f979a-131">次に、リソース グループに戻り、`nic1`、`IP configurations` の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="f979a-131">Then, go back to the resource group and click `nic1` then click `IP configurations`.</span></span> <span data-ttu-id="f979a-132">`IP configurations` セクションで、`subnet` が `firstSubnet (10.0.0.0/24)` に設定されています。</span><span class="sxs-lookup"><span data-stu-id="f979a-132">In the `IP configurations` section, the `subnet` is set to `firstSubnet (10.0.0.0/24)`.</span></span>

![nic1 IP 構成設定](../_images/nic1-ipconfigurations.png)

<span data-ttu-id="f979a-134">元の `firstVNet` が、再作成ではなく更新されています。</span><span class="sxs-lookup"><span data-stu-id="f979a-134">The original `firstVNet` has been updated instead of recreated.</span></span> <span data-ttu-id="f979a-135">`firstVNet` が再作成されているとしたら、`nic1` は `firstVNet` に関連付けられていません。</span><span class="sxs-lookup"><span data-stu-id="f979a-135">If `firstVNet` had been recreated, `nic1` would not be associated with `firstVNet`.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f979a-136">次の手順</span><span class="sxs-lookup"><span data-stu-id="f979a-136">Next steps</span></span>

* <span data-ttu-id="f979a-137">パラメーター値の有無などの条件に基づいてリソースをデプロイする方法を確認します。</span><span class="sxs-lookup"><span data-stu-id="f979a-137">Learn how deploy a resource based on a condition, such as whether a parameter value is present.</span></span> <span data-ttu-id="f979a-138">「[Azure Resource Manager テンプレートのリソースを条件付きでデプロイする](./conditional-deploy.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f979a-138">See [Conditionally deploy a resource in an Azure Resource Manager template](./conditional-deploy.md).</span></span>

[cli]: /cli/azure/?view=azure-cli-latest
[github]: https://github.com/mspnp/template-examples

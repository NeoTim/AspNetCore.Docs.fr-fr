---
ms.openlocfilehash: 1f8d3913c83aaf5fe6ec2cec482a30f0f066c16b
ms.sourcegitcommit: 34bf9fc6ea814c039401fca174642f0acb14be3c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/14/2019
ms.locfileid: "57841691"
---

> [!NOTE]
> <span data-ttu-id="cd5e2-101">Pour ce didacticiel, vous utilisez les fonctionnalités de *migrations* Entity Framework Core lorsque c’est possible.</span><span class="sxs-lookup"><span data-stu-id="cd5e2-101">For this tutorial you use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="cd5e2-102">Les migrations mettent à jour le schéma de la base de données pour qu’elle corresponde aux modifications dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="cd5e2-102">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="cd5e2-103">Toutefois, les migrations ne peuvent effectuer que les modifications prises en charge par le fournisseur EF Core, et les capacités du fournisseur SQLite sont limitées.</span><span class="sxs-lookup"><span data-stu-id="cd5e2-103">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="cd5e2-104">Par exemple, l’ajout d’une colonne est pris en charge, mais pas sa suppression ou sa modification.</span><span class="sxs-lookup"><span data-stu-id="cd5e2-104">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="cd5e2-105">Si vous créez une migration pour supprimer ou modifier une colonne, la commande `ef migrations add` réussit mais la commande `ef database update` échoue.</span><span class="sxs-lookup"><span data-stu-id="cd5e2-105">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="cd5e2-106">En raison de ces limitations, ce tutoriel n’utilise pas les migrations pour les modifications de schéma SQLite.</span><span class="sxs-lookup"><span data-stu-id="cd5e2-106">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="cd5e2-107">À la place, vous supprimez puis recréez la base de données quand le schéma change.</span><span class="sxs-lookup"><span data-stu-id="cd5e2-107">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="cd5e2-108">Pour remédier aux limitations de SQLite, vous devez écrire le code de migrations manuellement pour regénérer le tableau lorsqu’un élément est modifié.</span><span class="sxs-lookup"><span data-stu-id="cd5e2-108">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="cd5e2-109">La regénération d’un tableau implique :</span><span class="sxs-lookup"><span data-stu-id="cd5e2-109">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="cd5e2-110">La modification du nom du tableau existant.</span><span class="sxs-lookup"><span data-stu-id="cd5e2-110">Renaming the existing table.</span></span>
>* <span data-ttu-id="cd5e2-111">La création d’un nouveau tableau.</span><span class="sxs-lookup"><span data-stu-id="cd5e2-111">Creating a new table.</span></span>
>* <span data-ttu-id="cd5e2-112">La copie de données de l’ancien tableau vers le nouveau.</span><span class="sxs-lookup"><span data-stu-id="cd5e2-112">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="cd5e2-113">La suppression de l’ancien tableau.</span><span class="sxs-lookup"><span data-stu-id="cd5e2-113">Dropping the old table.</span></span>
>
><span data-ttu-id="cd5e2-114">Pour plus d'informations, reportez-vous aux ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="cd5e2-114">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="cd5e2-115">Limites d’un fournisseur de base de données EF Core SQLite</span><span class="sxs-lookup"><span data-stu-id="cd5e2-115">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="cd5e2-116">Personnaliser le code de migration</span><span class="sxs-lookup"><span data-stu-id="cd5e2-116">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="cd5e2-117">Amorçage des données</span><span class="sxs-lookup"><span data-stu-id="cd5e2-117">Data seeding</span></span>](/ef/core/modeling/data-seeding)
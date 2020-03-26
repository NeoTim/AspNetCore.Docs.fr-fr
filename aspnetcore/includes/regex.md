> [!WARNING]
> <span data-ttu-id="0f968-101">Lorsque vous utilisez <xref:System.Text.RegularExpressions> pour traiter une entrée non fiable, transmettez un délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="0f968-101">When using <xref:System.Text.RegularExpressions> to process untrusted input, pass a timeout.</span></span> <span data-ttu-id="0f968-102">Un utilisateur malveillant peut fournir une entrée à `RegularExpressions` provoquant une [attaque par déni de service](https://www.us-cert.gov/ncas/tips/ST04-015).</span><span class="sxs-lookup"><span data-stu-id="0f968-102">A malicious user can provide input to `RegularExpressions` causing a [Denial-of-Service attack](https://www.us-cert.gov/ncas/tips/ST04-015).</span></span> <span data-ttu-id="0f968-103">ASP.NET Core API d’infrastructure qui utilisent `RegularExpressions` passer un délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="0f968-103">ASP.NET Core framework APIs that use `RegularExpressions` pass a timeout.</span></span>
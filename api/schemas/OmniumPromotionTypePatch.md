# OmniumPromotionTypePatch

Promotion type data

**Fields:**

- `promotionType`: [`PromotionType`](PromotionType.md)
- `reward`: [`OmniumPromotionRewardPatch`](OmniumPromotionRewardPatch.md)
- `shippingMethods`: List<[`OmniumPromotionSelectedShipmentMethods`](OmniumPromotionSelectedShipmentMethods.md)>, nullable, max length 250 — Defines the shipping methods that the promotion is valid for (Only applies if PromotionType is ShippingPromotion).
- `amountCondition`: List<[`OmniumPromotionAmount`](OmniumPromotionAmount.md)>, nullable, max length 250 — Amount conditions for the promotion (Only applies if PromotionType is ShippingPromotion).
- `categoryAndBrandFilter`: [`OmniumCategoryAndBrandFilter`](OmniumCategoryAndBrandFilter.md)
- `promotionMultiBuyReward`: [`OmniumPromotionMultiBuyRewardPatch`](OmniumPromotionMultiBuyRewardPatch.md)
- `discountedCategories`: List<[`OmniumProductCategory`](OmniumProductCategory.md)>, nullable, max length 250 — Discounted categories for Mix and match promotions. E.g buy one item of category A, get one item of category B free. (Only applies if PromotionType is MultiBuyPromotion).
- `discountedProducts`: List<[`OmniumPromotionProduct`](OmniumPromotionProduct.md)>, nullable, max length 10000 — Discounted products for Mix and match promotions. E.g buy one item of product or category A, get one item of product B free. (Only applies if PromotionType is MultiBuyPromotion).
- `productSearchRequest`: [`OmniumProductSearchRequest`](OmniumProductSearchRequest.md)
- `minQuantity`: integer (int32), nullable — Minimum total quantity of items required in the order (Only applies if PromotionType is OrderAmountPromotion).
- `conditionOperator`: [`OmniumConditionOperator`](OmniumConditionOperator.md)
- `includeProductsExcludedFromPromotions`: boolean, nullable — When true, products marked ExcludeFromPromotions still count towards the discount, so both the discount basis and the amount/quantity conditions cover the whole order (Only applies if PromotionType is OrderAmountPromotion). Order lines with a locked price or a selected unit are still left out.
- `promotionKitReward`: [`OmniumPromotionKitRewardPatch`](OmniumPromotionKitRewardPatch.md)
- `priceListId`: string, nullable — The Id of the price list to use for this promotion (Applies to PriceListPromotion and CostPricePromotion). For CostPricePromotion, the price list provides cost prices used as the discount base.
- `markupPercentage`: number (decimal), nullable — Percentage markup added to the cost price (Only applies if PromotionType is CostPricePromotion).
- `useStoreCostPrice`: boolean, nullable — When true, uses the StoreCostPrice from the price list item instead of the supplier cost price (Only applies if PromotionType is CostPricePromotion).

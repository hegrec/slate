# Product SKUs

## Product SKU Object - Properties

| Name | Type | Description |
| --- | --- | --- |
| id | int | |
| product_id | int | |
| sku | string | The unique SKU (stock keeping unit). |
| price | decimal | This SKU's base price on the storefront. If this value is null, the product's default price (set in the Product resource's `price` field) will be used as the base price. |
| adjusted_price | decimal | The SKU's price on the storefront – after the product's base price is inherited, and/or any option set or any product rules are applied. This property is READ-ONLY. |
| cost_price | decimal | The product's cost price. |
| upc | string | The UPC (Universal Product Code) for this product combination. |
| inventory_level | int | The inventory level for the product. |
| inventory_warning_level | int | The inventory warning level for the product .|
| bin_picking_number | string | The BIN picking number. |
| weight | decimal | This SKU's base weight on the storefront. If this value is null, the product's default weight (set in the Product resource's weight field) will be used as the base weight. |
| adjusted_weight | decimal | This SKU's weight on the storefront – after the product's base weight is inherited, and/or any option set or any product rules are applied. This property is READ-ONLY. |
| is_purchasing_disabled | boolean | if true, this prohibits purchasing of the SKU. | 
| purchasing_disabled_message | string | The message to display if purchasing is disabled on this SKU. | 
| image_file | string | The image that will be displayed when this SKU is selected on the storefront. When updating a SKU image, send the publicly accessible URL. Supported image formats are JPEG, PNG, and GIF. | 
| options | object_array | This is an object {"product_option_id": int, "option_value_id":int} |

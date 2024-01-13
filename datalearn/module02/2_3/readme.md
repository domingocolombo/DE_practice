# Заливка в СУБД данных Superstore

## Наблюдения

**product_id**
Выглядит как потенциальный первичный ключ при вынесении данных в таблицу измерений. Однако, у одного id могут быть несколько названий.
**Примеры**


|product_id|product_name|
|-|-|
|FUR-BO-10002213|DMI Eclipse Executive Suite Bookcases|
| |Sauder Forest Hills Library, Woodland Oak Finish|
|FUR-CH-10001146|Global Task Chair, Black|
| |Global Value Mid-Back Manager's Chair, Gray|


Проверял так:
```sql
SELECT
			product_id,
			category,
			subcategory,
			product_name
FROM orders
WHERE product_id IN 
	-- Запрос 2. Ищем product_id с COUNT(distinct(product_name))>1
(
	SELECT
		product_id
	FROM 
	-- Запрос 1. Забираем только нужные столбцы
		(SELECT DISTINCT
			product_id,
			category,
			subcategory,
			product_name
		FROM orders)
	
	GROUP BY product_id
	HAVING COUNT(DISTINCT(product_name))>1
)
GROUP BY 
			product_id,
			category,
			subcategory,
			product_name
ORDER BY product_id, product_name
;
```

Как вариант - попробовать взять product_name можно по максимальному значению order_date


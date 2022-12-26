### Task
Таблица discounts содержит скидки discount на все товары в разрезе дня (day).
  
Таблица pricelist содержит изменения цены price на товар в разрезе
различных валют (currency от 1 до 10). Изменение цены записывается в тот
час, когда цена изменилась (hour от 1 до 300). Day - тот день, когда цена
изменилась (от 1 до 13).
  
Таблица quotes содержит котировки (quote) валюты (currency) к базовой 
валюте на каждый час (от 1 до 300).
  
Отобразить минимальную (среди всех валют) базовую цену в конце каждого дня 
с учетом дневной скидки для товаров 50, 150, 300 за дни 3, 7, 12 

### SQL query
```
with products as (
	select product_id, currency, day, hour, min(price) as minPrice from pricelist group by product_id, currency, day, hour
),
productsWithBasePrice as (
	select product_id, products.currency, products.day,discount, products.hour, minPrice, quote, (minPrice * quote * discount) as basePrice from products
	left join quotes
	on products.currency = quotes.currency AND products.hour = quotes.hour
	left join discounts
	on discounts.day = products.day
)
select product_id, day, min(basePrice) as minBasePrice from productsWithBasePrice * group by product_id, day
having (product_id = 50 or product_id = 150 or product_id = 300) AND (day = 3 or day = 7 or day = 12);

```

### Result
| product_id | day | minbaseprice |
|------------|-----|--------------|
| 300        | 3   | 0.16968      |
| 50         | 3   | 7.095996     |
| 300        | 12  | 0.646488     |
| 150        | 3   | 0.735072     |
| 150        | 7   | 0.024255     |
| 300        | 7   | 0.20449      |

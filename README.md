# Домашнее задание к занятию "`Введение в SQL`" - `Левковец Ирина`


### Инструкция по выполнению домашнего задания

   1. Сделайте `fork` данного репозитория к себе в Github и переименуйте его по названию или номеру занятия, например, https://github.com/имя-вашего-репозитория/git-hw или  https://github.com/имя-вашего-репозитория/7-1-ansible-hw).
   2. Выполните клонирование данного репозитория к себе на ПК с помощью команды `git clone`.
   3. Выполните домашнее задание и заполните у себя локально этот файл README.md:
      - впишите вверху название занятия и вашу фамилию и имя
      - в каждом задании добавьте решение в требуемом виде (текст/код/скриншоты/ссылка)
      - для корректного добавления скриншотов воспользуйтесь [инструкцией "Как вставить скриншот в шаблон с решением](https://github.com/netology-code/sys-pattern-homework/blob/main/screen-instruction.md)
      - при оформлении используйте возможности языка разметки md (коротко об этом можно посмотреть в [инструкции  по MarkDown](https://github.com/netology-code/sys-pattern-homework/blob/main/md-instruction.md))
   4. После завершения работы над домашним заданием сделайте коммит (`git commit -m "comment"`) и отправьте его на Github (`git push origin`);
   5. Для проверки домашнего задания преподавателем в личном кабинете прикрепите и отправьте ссылку на решение в виде md-файла в вашем Github.
   6. Любые вопросы по выполнению заданий спрашивайте в чате учебной группы и/или в разделе “Вопросы по заданию” в личном кабинете.
   
Желаем успехов в выполнении домашнего задания!
   
### Дополнительные материалы, которые могут быть полезны для выполнения задания

1. [Руководство по оформлению Markdown файлов](https://gist.github.com/Jekins/2bf2d0638163f1294637#Code)

---

### Задание 1
Одним запросом получите информацию о магазине, в котором обслуживается более 300 покупателей,
и выведите в результат следующую информацию:
- фамилия и имя сотрудника из этого магазина;
- город нахождения магазина;
- количество пользователей, закреплённых в этом магазине.
```sql
select store.store_id, staff.first_name, staff.last_name, c2.city, count(customer_id) as num_customers
from store
join sakila.customer c on store.store_id = c.store_id
join sakila.address a on store.address_id = a.address_id
join sakila.city c2 on a.city_id = c2.city_id
join staff on store.manager_staff_id = staff.staff_id
group by c.store_id
having num_customers > 300;
```

### Задание 2
Получите количество фильмов, продолжительность которых больше средней продолжительности всех фильмов.
```sql
select count(*) as film_count
from film
where film.length > (
    select avg(film.length)
    from film
    );
```

### Задание 3
Получите информацию, за какой месяц была получена наибольшая сумма платежей, и добавьте информацию по количеству аренд за этот месяц.
```sql
select monthname(payment_date) as month, sum(amount) as total_amount, count(payment.rental_id) from payment
group by monthname(payment_date)
order by sum(amount) desc
limit 1;
```

### Задание 4*
Посчитайте количество продаж, выполненных каждым продавцом. Добавьте вычисляемую колонку «Премия».
Если количество продаж превышает 8000, то значение в колонке будет «Да», иначе должно быть значение «Нет».
```sql
select concat(s.first_name, ' ', s.last_name) as `Продавец`, IF(count(rental_id) > 8000, 'Да', 'Нет') as `Премия`
from payment
join sakila.staff s on payment.staff_id = s.staff_id
group by payment.staff_id;
```

### Задание 5*
Найдите фильмы, которые ни разу не брали в аренду.
```sql
select f.title
from rental
join sakila.inventory i on rental.inventory_id = i.inventory_id
right join sakila.film f on i.film_id = f.film_id
where rental.inventory_id is null;
```
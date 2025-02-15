### Emails Sent by Month
Порахувати відсоток імейлів від загальної кількості, що було відправлено кожному акаунту в рамках кожного місяця
  (кількість листів, відправлених акаунту за місяць / загальну кількість листів, відправлених за місяць всім акаунтам).
Визначити дату першого та останнього надсилання імейлів для кожного акаунту у місяці.

```sql
select
sent_month,
id_account,
letter_count * 100 /
sum (letter_count) OVER () AS sent_msg_percent_from_this_month,
first_sent_date,
last_sent_date
from (
select
    EXTRACT(month
    FROM
      DATE_add(sss.date, interval ems.sent_date DAY )) sent_month,
      ems.id_account id_account,
      count(ems.id_account) letter_count,
      min (DATE_add(sss.date, interval ems.sent_date DAY )) first_sent_date,
      max (DATE_add(sss.date, interval ems.sent_date DAY )) last_sent_date
from `DA.email_sent` as ems
 join `DA.account_session` acs
 on acs.account_id = ems.id_account
 join `DA.session` sss
 on sss.ga_session_id = acs.ga_session_id
group by ems.id_account, 1) acounts_letters
order by sent_msg_percent_from_this_month desc
limit 100
```
![result](images/windows_rslt.png)

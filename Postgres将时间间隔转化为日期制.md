### Postgres 将时间间隔(秒数的形式)转化为日期制  

表中有一列存的是两个时间的间隔 

时间间隔为9秒 ,在查阅的时候想要显示成 (00:00:09)怎么办?

```sql
select("(mycolumn * interval '1 sec')")
```



详情: [stackflow](https://stackoverflow.com/questions/2905692/postgresql-how-to-convert-seconds-in-a-numeric-field-to-hhmmss)
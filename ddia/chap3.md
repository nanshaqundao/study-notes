# Data Storage and Search

## Simple Database
key-value pair: simply save and query for the value saved

```
db_set() {
    echo "$1,$2" >> database
}

db_get() {
    grep "^$1," database | sed -d "s/^$1,//" | tail -n 1
}

nash@LAPTOP-SP9KAOQ6:~$ db_set 12 '{"name":"London","attractions":["Big Ben", "London Eye"]}'
nash@LAPTOP-SP9KAOQ6:~$ db_get 12
{"name":"London","attractions":["Big Ben", "London Eye"]}
nash@LAPTOP-SP9KAOQ6:~$


```
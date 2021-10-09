# This is to demonstrate how overlayer file system works

- on host machine (wsl2 windows10 docker installed)

- ```
  mkdir upper lower merged work
  echo "from lower" > lower/in_lower.txt
  echo "from upper" > upper/in_upper.txt
  echo "from lower" > lower/in_both.txt 
  echo "from upper" > upper/in_both.txt 
  sudo mount -t overlay overlay -o lowerdir=`pwd`/lower,upperdir=`pwd`/upper,workdir=`pwd`/work `pwd`/merged
  ```

- now go to "merged" directory

- ```
  nash@LAPTOP-SP9KAOQ6:~$ cd merged/
  nash@LAPTOP-SP9KAOQ6:~/merged$ ls
  in_both.txt  in_lower.txt  in_upper.txt
  nash@LAPTOP-SP9KAOQ6:~/merged$ cat in_lower.txt
  from lower
  nash@LAPTOP-SP9KAOQ6:~/merged$ cat in_upper.txt
  from upper
  nash@LAPTOP-SP9KAOQ6:~/merged$ cat in_both.txt
  from upper
  ```

- you can see the "merged" is taken from upper layer
# 课程基本信息

课程网站：https://missing.csail.mit.edu/2020/

课程视频：https://www.youtube.com/playlist?list=PLyzOVJj3bHQuloKGG59rS43e29ro7I57J

# 课程笔记

## Lecture 1. Course overview + the shell

### command

*pwd* - print working directory

*cd* - change directory

*~(tilde)* - home directory

*-(dash)* - previous directory

*--help* - show the help info

*-l* - list the detail info 

*man* - manual page 

*<* - rewire the input stream

*\>* - rewire the output stream(overwrite)

*>>* - rewire the output stream(append)

*cat* - concatenate

*|* - pipe

*sudo su* - root

*sudo tee*

*xdg-open*

### Exercises

2. 
```shell
cd ../../tmp
mkdir missing
```

3. 
```shell
man touch
```

4. 
```shell
cd missing/
touch semester
```

5. 
```shell
echo '#!/bin/sh' > semester
echo 'curl --head --silent https://missing.csail.mit.edu' >> semester
```

6. 
``` shell
./semester
ls -l semester
```

7. 
``` shell
sh semester
```

8. 
``` shell
man chmod
```

9. 
``` shell
chmod 755 ./semester
./semester
```

10. 
``` shell
./semester | grep last-modified > ~/last-modified.txt
cat ~/last-modified.txt
```

11. 
``` shell
cat /sys/class/power_supply/battery/capacity
``` 

## Lecture 2. Shell Tools and Scripting
*$0* - the name of the script

*$1 - $9* - the args that the bash script takes

*$?* - th error code from the previous command

*$_* - the last arg of the previous command

*!!* - the last command

*$#* - the number of the args

*\$$* - the pid of the running command

*$@* - the all args 

*2>* - redirect the error standard stream

*$() <()* - replace cmd

### Exercises
1. 
```shell
man ls
ls -a -t -h --color=auto -l
```
2.  
```shell
#!/bin/bash

marco () {
        tmp_path=$(pwd)
}

polo () {
        cd "$tmp_path"
}
```

3.  
```shell
#!/bin/bash

ex3_path="/home/yggycs"

num=1
$ex3_path/ex3.sh > "$ex3_path/ex3.log"

while [ "$?" -ne 1 ]; do
        num=$(($num+1))
        $ex3_path/ex3.sh >> "$ex3_path/ex3.log" 2> "$ex3_path/ex3err.log"
done

echo "$num"
cat "$ex3_path/ex3.log" "$ex3_path/ex3err.log"
```

4.  
![alt text](figure/fig1.png)
```shell
find . -name '*.html' | xargs -d '\n' tar -cvf html.tar
```
![alt text](figure/fig2.png)

5.  
```shell
find . -type f | xargs -d '\n' ls -t -l -h
```

## Lecture 3. Editors (Vim)

### Exercises
1. 
```shell
vimtutor
```

2. 
```shell
touch .vimrc
```

3. 
```shell
mkdir -p ~/.vim/pack/vendor/start
cd ~/.vim/pack/vendor/start; git clone https://github.com/ctrlpvim/ctrlp.vim

# vim command
:Ctrl-P

# ~/.vimrc
# let g:ctrlp_map = '<c-p>'
# let g:ctrlp_cmd = 'CtrlP'
```

6. 
- Chrome 插件： Vimium

- 配置 vscode + vim 编辑器

8. 
- *Gdd*, *ggdd* delete first and last lines
- Macro to format a single element (register *e*)
    - Go to line with *\<name>*
    - *qe^r"f>s": "\<ESC>f\<C"\<ESC>q*
- Macro to format a person
    - Go to line with *\<person>*
    - *qpS{\<ESC>j@eA,\<ESC>j@ejS},<ESC>q*
- Macro to format a person and go to the next person
    - Go to line with *\<person>*
    - *qq@pjq*
- Execute macro until end of file
*999@q*
- Manually remove last *,* and add *[* and *]* delimiters

## Lecture 4. Data Wrangling

*sed* - a stream editor

*sed -E*

*capture groups*

*wc* - word counting

*sort*

*uniq*

*head -n*

*tail -n*

*awk* - column stream editor

*paste*

*bc*

*R* - R programming language

*gnuplot* - plotter

*xargs*

*ffmpeg* 

*convert*

*-* - turn the file output to a stream output

### Exercise
2.  
``` shell
cat /usr/share/dict/words | tr [:upper:] [:lower:] | grep -E "^([b-z]*a){3}[a-z]*$" | grep -v "'s$" | wc -l

cat /usr/share/dict/words | tr [:upper:] [:lower:] | grep -E "^([b-z]*a){3}[a-z]*$" | grep -v "'s$" | sed -E "s/.*([a-z]{2})$/\1/" | sort | uniq -c | sort -nk1,1 | tail -n3

cat /usr/share/dict/words | tr [:upper:] [:lower:] | grep -E "^([b-z]*a){3}[a-z]*$" | grep -v "'s$" | sort | uniq | wc -l

# tmp0.sh
#!/bin/bash
for i in {a..z}; do
    for j in {a..z}; do
        echo "$i$j"
    done
done

./tmp0.sh > tmp0.log
cat /usr/share/dict/words | tr [:upper:] [:lower:] | grep -E "^([b-z]*a){3}[a-z]*$" | grep -v "'s$" | uniq > tmp1.log 
cat tmp0.log tmp1.log | sort | uniq -u
```

3. 输出流目的文件会先被清空导致命令运行不正确，不是 sed 特有的，而是使用 > 带来的。 
``` shell
sed -i.bak "s/REGEX/SUBSTITUTION/" input.txt
```

4. 
``` shell
journalctl | sed -E "s/Startup finished in (\d+)ms \./\1/" | tail -n10 | sort | tail -n1
journalctl | sed -E "s/Startup finished in (\d+)ms \./\1/" | tail -n10 | sort | head -n1
journalctl | sed -E "s/Startup finished in (\d+)ms \./\1/" | tail -n10 | paste -sd+ | bc -l | awk '{print $1/10}'
journalctl | sed -E "s/Startup finished in (\d+)ms \./\1/" | tail -n10 | sort | head -n6 | tail -2 | paste -sd+ | bc -l | awk '{print $1/2}'
```

5. 
``` shell
journalctl -b 0 > tmp.log
journalctl -b -1 >> tmp.log
journalctl -b -2 >> tmp.log
cat tmp.log | grep "Startup finished in" | sed -E "(s/.*in).*s \.$/\1/" | sort | uniq -c | awk '$1!=3 { print }'
```

6. 
``` shell
curl "https://ucr.fbi.gov/crime-in-the-u.s/2016/crime-in-the-u.s.-2016/topic-pages/tables/table-1" > data.html
cat data.html | pup ".group0 text{}" > column1.log
cat data.html | pup ".group1 text{}" > column2.log
paste column1.log column2.log | awk '{ print $1 }' | sort -n | sed -n '1p;$p;'

paste column1.log column2.log | sed -E 's/(.*)\s(.*)$/\1-(\2)/' | paste -sd+ | bc -l
```

## Lecture 5. Command-line Environment

## Lecture 6. Version Control (Git)

## Lecture 7. Debugging and Profiling

## Lecture 8. Metaprogramming

## Lecture 9. Security and Cryptography

## Lecture 10. Potpourri

## Lecture 11. Q&A
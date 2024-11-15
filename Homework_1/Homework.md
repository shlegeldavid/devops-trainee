1. Узнать IP-адрес интерфейса, подключенного к сети Интернет\n
   IP - 188.227.32.10 
   Узнать можно командой hostname -I или ip route get 1.1.1.1 (8.8.8.8, что угодно)


2. Создать именованный пайп (named pipe). Вывести в файл через созданный pipe вывод команды ss -plnt
   mkfifo pipe
   ss -plnt > pipe //на первом терминале
   cat < pipe | tee output.md //на втором терминале (можно и через >)
   Если подразумевается работа на одном терминале:
   ss -plnt > pipe | cat < pipe > output.md


3. При помощи именованного пайпа заархивировать всё, что в него отправляем. Например, содержимое файла /var/log/messages. На выходе получить архив tar или любой другой
   mkfifo /tmp/archivation_pipe
   tar czf /tmp/messages.tar.gz -C /var/log messages < /tmp/archivation_pipe &
   cat /var/log/messages > /tmp/archivation_pipe

4. Вывести дату в unixtime. На вход команды date через пайп подать свой формат выводимой даты
   date "+%Y-%m-%d %H:%M:%S" | xargs -I {} date -d "{}" +%s //через пайп

   date "+%Y-%m-%d %H:%M:%S" > pipe &
   cat pipe | xargs -I {} date -d "{}" +%s //через именованный пайп


5. При помощи HEREDOC записать в файл многострочное сообщение
   cat << EOF > many_strings.md
   1 HI
   2 Hello
   3 Welcome
   4 Goodbye
   EOF

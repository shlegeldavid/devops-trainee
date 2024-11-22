1. Отразить 7 последних строк из wtmp:

   Чтобы отразить 7 последних строк из wtmp, можно воспользоваться командой last.
   
   ```bash
   last | head -n 7
   ```
   Полученный вывод:
   ```bash
   [root@DevOps-training log]# last | head -n 7
   root     pts/0        80.85.241.13     Fri Nov 22 13:57   still logged in
   root     pts/0        80.85.241.13     Fri Nov 15 13:01 - 13:14  (00:13)
   reboot   system boot  5.4.17-2136.310. Fri Nov 15 13:00   still running
   root     pts/0        80.85.241.13     Fri Nov 15 10:54 - crash  (02:06)
   root     pts/0        80.85.241.13     Fri Nov 15 10:21 - 10:41  (00:19)
   root     pts/2        80.85.241.13     Fri Nov 15 10:02 - 10:12  (00:10)
   root     pts/1        80.85.241.13     Fri Nov 15 10:01 - 10:16  (00:14)
   ```


2. При помощи logger записать в общий журнал послание добра и мира посланникам из Альфа-Центавра с facility kernel.
   
   ```bash
   logger -p kern.info "Послание добра и мира посланникам из Альфа-Центавра"
   ```
   
   kern.info указывает, что это информационное сообщение, можно также, например kern.debug, если это дебаг сообщение.


3. 

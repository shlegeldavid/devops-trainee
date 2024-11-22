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
   Проверить, что сообщение успешно записалось можно так:

   ```bash
   journactl | tail -n 5
   ```

   И увидим там вывод:
   ```bash
   [root@DevOps-training log]# journalctl | tail -n 5
   Nov 22 14:20:06 DevOps-training.local sshd[136185]: Received disconnect from 148.72.246.251 port 38134:11: Bye Bye [preauth]
   Nov 22 14:20:06 DevOps-training.local sshd[136185]: Disconnected from invalid user web 148.72.246.251 port 38134 [preauth]
   Nov 22 14:20:29 DevOps-training.local sshd[136188]: Invalid user xavier from 164.92.71.85 port 51564
   Nov 22 14:20:29 DevOps-training.local sshd[136188]: Connection closed by invalid user xavier 164.92.71.85 port 51564 [preauth]
   Nov 22 14:20:59 DevOps-training.local root[136191]: Послание добра и мира посланникам из Альфа-Центавра
   ```


3. Посмотреть последние 25 строчек лога сервиса systemd-logind.

   ```bash
   journalctl -u systemd-logind -n 25
   ```

   Такой вывод:

   ```bash
   -- Logs begin at Fri 2024-11-15 13:00:44 UTC, end at Fri 2024-11-22 14:32:25 UTC. --
   Nov 15 13:00:49 DevOps-training.local systemd[1]: Starting Login Service...
   Nov 15 13:00:49 DevOps-training.local systemd-logind[633]: New seat seat0.
   Nov 15 13:00:49 DevOps-training.local systemd-logind[633]: Watching system buttons on /dev/input/event0 (Power Button)
   Nov 15 13:00:49 DevOps-training.local systemd-logind[633]: Watching system buttons on /dev/input/event1 (AT Translated Set 2 keyboard)
   Nov 15 13:00:49 DevOps-training.local systemd[1]: Started Login Service.
   Nov 15 13:01:02 DevOps-training.local systemd-logind[633]: New session 1 of user root.
   Nov 15 13:08:05 DevOps-training.local systemd-logind[633]: Watching system buttons on /dev/input/event0 (Power Button)
   Nov 15 13:08:05 DevOps-training.local systemd-logind[633]: Watching system buttons on /dev/input/event1 (AT Translated Set 2 keyboard)
   Nov 15 13:08:26 DevOps-training.local systemd-logind[633]: Watching system buttons on /dev/input/event0 (Power Button)
   Nov 15 13:08:26 DevOps-training.local systemd-logind[633]: Watching system buttons on /dev/input/event1 (AT Translated Set 2 keyboard)
   Nov 15 13:14:09 DevOps-training.local systemd-logind[633]: Session 1 logged out. Waiting for processes to exit.
   Nov 15 13:14:09 DevOps-training.local systemd-logind[633]: Removed session 1.
   Nov 22 13:57:30 DevOps-training.local systemd-logind[633]: New session 3 of user root. 
   ```


4. Создать конфигурацию syslog для отправки сообщений уровня info в файл /var/log/my.log

   В Oracle Linux 8 используется демон rsyslog, так что работаем с ним.

   ```bash
   nano /etc/rsyslog.d/my-log.conf
   ```

   Чтобы не менять кофигурацию rsyslog.conf напрямую, создаем свой файл конфигурации и в нем прописываем:

   ```bash
   *.=info /var/log/my.log
   ```

   Далее:

   ```bash
   touch /var/log/my.log
   chmod 640 /var/log/my.log
   systemctl restart syslog
   ```

   Теперь можем проверить работоспособность просто читая файл my.log:

   ```bash
   [root@DevOps-training log]# cat /var/log/my.log | tail -n 3
   Nov 22 14:46:05 DevOps-training sshd[136448]: Disconnected from invalid user server2 148.72.246.251 port 60264 [preauth]
   Nov 22 14:46:14 DevOps-training sshd[136451]: Invalid user default from 92.255.57.132 port 52168
   Nov 22 14:46:14 DevOps-training sshd[136451]: Connection closed by invalid user default 92.255.57.132 port 52168 [preauth]
   ```

5. Все события ssh записывать параллельно в отдельный файл, производить ротацию каждые сутки или по размеру (не более 1 мегабайта), 
   всего 10 файлов в ротации.

   Для этого также создадим конфигурацию для rsyslog.

   ```bash
   nano /etc/rsyslog.d/my-ssh.conf
    auth,authpriv.* /var/log/ssh.log
   touch /var/log/ssh.log
   chmod 640 /var/log/ssh.log
   ```

   Далее настраиваем ротацию логов, используя logrotate.

   ```bash
   nano /etc/logrotate.d/ssh
   ```

   И прописываем в файле конфигурацию для ротации:

   ```bash
   /var/log/ssh.log {
    daily
    rotate 10
    size 1M
    notifempty
    create 640
   }
   ```

   Далее проверяем, создана ли ротация:

   ```bash
   logrotate -d /etc/logrotate.d/ssh #ошибок нет
   logrotate /etc/logrotate.d/ssh
   ```

   Все, ротация файлов успешно создана, новые записи поступают в ssh.log, ротация произойдет на след. день или при достижении 1 Мб веса файла.


6. Вывести сообщения с последнего запуска системы. Вывести сообщения не старше одного часа.

   Чтобы вывести сообщения с последнего запуска системы, надо:

   ```bash
   journalctl -b
   ```

   Вывод большой, приведу просто пример вывода, не весь:

   ```bash
   [root@DevOps-training ~]# journalctl -b
   -- Logs begin at Fri 2024-11-15 13:00:44 UTC, end at Fri 2024-11-22 16:34:04 UTC. --
   Nov 15 13:00:44 localhost.localdomain kernel: Linux version 5.4.17-2136.310.7.1.el8uek.x86_64 (mockbuild@host-100-100-224-44) (gcc versio>
   Nov 15 13:00:44 localhost.localdomain kernel: Command line: BOOT_IMAGE=(hd0,gpt2)/boot/vmlinuz-5.4.17-2136.310.7.1.el8uek.x86_64 root=UUI>
   Nov 15 13:00:44 localhost.localdomain kernel: x86/fpu: Supporting XSAVE feature 0x001: 'x87 floating point registers'
   Nov 15 13:00:44 localhost.localdomain kernel: x86/fpu: Supporting XSAVE feature 0x002: 'SSE registers'
   Nov 15 13:00:44 localhost.localdomain kernel: x86/fpu: Supporting XSAVE feature 0x004: 'AVX registers'
   Nov 15 13:00:44 localhost.localdomain kernel: x86/fpu: Supporting XSAVE feature 0x020: 'AVX-512 opmask'
   Nov 15 13:00:44 localhost.localdomain kernel: x86/fpu: Supporting XSAVE feature 0x040: 'AVX-512 Hi256'
   Nov 15 13:00:44 localhost.localdomain kernel: x86/fpu: Supporting XSAVE feature 0x080: 'AVX-512 ZMM_Hi256'
   Nov 15 13:00:44 localhost.localdomain kernel: x86/fpu: xstate_offset[2]:  576, xstate_sizes[2]:  256
   Nov 15 13:00:44 localhost.localdomain kernel: x86/fpu: xstate_offset[5]: 1088, xstate_sizes[5]:   64
   Nov 15 13:00:44 localhost.localdomain kernel: x86/fpu: xstate_offset[6]: 1152, xstate_sizes[6]:  512
   Nov 15 13:00:44 localhost.localdomain kernel: x86/fpu: xstate_offset[7]: 1664, xstate_sizes[7]: 1024
   ```

   Чтобы вывести сообщения не старше одного часа:

   ```bash
   journalctl --since "1 hour ago"
   ```

   Вывод:

   ```bash
   [root@DevOps-training ~]# journalctl --since "1 hour ago"
   -- Logs begin at Fri 2024-11-15 13:00:44 UTC, end at Fri 2024-11-22 16:37:42 UTC. --
   Nov 22 15:37:50 DevOps-training.local sshd[136672]: Received disconnect from 128.199.95.60 port 38026:11: Bye Bye [preauth]
   Nov 22 15:37:50 DevOps-training.local sshd[136672]: Disconnected from authenticating user root 128.199.95.60 port 38026 [preauth]
   Nov 22 15:38:17 DevOps-training.local sshd[136675]: Received disconnect from 181.212.81.228 port 45146:11: Bye Bye [preauth]
   Nov 22 15:38:17 DevOps-training.local sshd[136675]: Disconnected from authenticating user root 181.212.81.228 port 45146 [preauth]
   Nov 22 15:38:38 DevOps-training.local sshd[136678]: Received disconnect from 46.101.82.89 port 58190:11: Bye Bye [preauth]
   Nov 22 15:38:38 DevOps-training.local sshd[136678]: Disconnected from authenticating user root 46.101.82.89 port 58190 [preauth]
   ```

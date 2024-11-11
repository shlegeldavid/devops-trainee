1. IP - 188.227.32.10 
Узнать можно командой hostname -I или ip route get 1.1.1.1 (8.8.8.8, что угодно)


2. mkfifo pipe
   ss -plnt > pipe //на первом терминале
   cat < pipe | tee output.md //на втором терминале (можно и через >)
   Если подразумевается работа на одном терминале:
   ss -plnt > pipe | cat < pipe > output.md


3. 

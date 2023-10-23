# lesson7

При выполнении задания использовал образ centos/7 - v. 1804.02

Все этапы производились согласно методичке

**Домашнее задание**

1. Попасть в систему без пароля несколькими способами.
2. Установить систему с LVM, после чего переименовать VG.
3. Добавить модуль в initrd.

***1. вход в систему без пароля***

Существует 3 способа попасть в систему и заменить пароль суперпользователя root

В каждом способе главное удалить запись **console=tty0 console=ttS0,115200n8**

1) rw init=/bin/sh
2) rw init=/sysroot/bin/sh
3) rd.break console=tty1

Вот один из вариантов входа

![image](https://github.com/movik242/lesson7/assets/143793993/18cbb031-a235-4673-9157-f769e2fff087)

Далее делаю монтрование или сразу (если rw) захожу в chroot

      mount -o remount,rw /sysroot
      chroot /sysroot
      passwd
      touch /.autorelabel

![image](https://github.com/movik242/lesson7/assets/143793993/cce36408-56d9-4a7a-9fb4-8b0712828921)

Выводы: После первой перезагрузки постоянно получал сообщение login incorrect, проблема решилась после добавления опции при загрузке **enforcing=0**. Оказывается это маркировка диска SElinux включенная по умолчанию. Поэтому после успешного входа в систему изменяю grub, указывая параметр в конце строки с linux16

      vi /etc/default/grub
      grub2-mkconfig -o /boot/grub2/grub.cfg

Далее любое изменение пароля root через grub, нормально заходит в систему


***2. Переименовать VG в системе LVM***

Проверяю и переименовываю

![image](https://github.com/movik242/lesson7/assets/143793993/fcf64998-74f3-4237-9821-a4697cdc19ad)


Правлю /etc/fstab, /etc/default/grub, /boot/grub2/grub.cfg. Пересоздаю initrd image


![image](https://github.com/movik242/lesson7/assets/143793993/38ece4bb-60e8-4a5b-a7e1-80d7493f6b86)


Перезагружаюсь и проверяю LVM

![image](https://github.com/movik242/lesson7/assets/143793993/d6b65ad9-82bd-4c75-83df-5a0b3d5e9623)



***3. Добавить модуль в initrd***

Создаю папку 01test  и записавыю туда скрипты

![image](https://github.com/movik242/lesson7/assets/143793993/0239441e-5e19-4187-897d-c0a002b3581d)

Пересобираею образ initrd и проверяю какие модули загружены в образ

      dracut -f -v


![image](https://github.com/movik242/lesson7/assets/143793993/46b7a195-49f2-438c-ae65-26caf96a67bf)


При загрузке удаляю опции rghb и quiet, чтобы видеть загрузку.
Итог


![image](https://github.com/movik242/lesson7/assets/143793993/a1edff15-028a-474b-8061-9f16b283d14f)


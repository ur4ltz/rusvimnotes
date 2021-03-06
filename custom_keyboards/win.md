# AutoHotkey

## Рецепты

### Работа CapsLock как Ctrl/Escape

https://gist.github.com/sedm0784/4443120

### Идемпотентное переключение раскладки

Используем AutoHotkey:

```
SetDefaultKeyboard(LocaleID){
    Static SPI_SETDEFAULTINPUTLANG := 0x005A, SPIF_SENDWININICHANGE := 2

    Lan := DllCall("LoadKeyboardLayout", "Str", Format("{:08x}", LocaleID), "Int", 0)
    VarSetCapacity(binaryLocaleID, 4, 0)
    NumPut(LocaleID, binaryLocaleID)
    DllCall("SystemParametersInfo", "UInt", SPI_SETDEFAULTINPUTLANG, "UInt", 0, "UPtr", &binaryLocaleID, "UInt", SPIF_SENDWININICHANGE)

    WinGet, windows, List
    Loop % windows {
        PostMessage 0x50, 0, % Lan, , % "ahk_id " windows%A_Index%
    }
    }

LWin::SetDefaultKeyboard(0xa0000409)

RWin::SetDefaultKeyboard(0x0419)
```

Обратите внимание, что это конфигурация моей машины, как адепта Colemak, поэтому
для вашего случая строка переключения на английский язык может выглядеть иначе:
`LWin::SetDefaultKeyboard(0x0409)`

### Хочу, чтобы пробел+Alt давал пробел, а не переключал язык

```
#Space::
Send, {Space}
return
```

### Привычные Emacs-like хоткеи в Chrome

https://superuser.com/questions/1253772/unix-shortcuts-on-windows-ctrl-p-ctrl-n-ctrl-f

### Источники вдохновения ваших AutoHotkey файлов

https://github.com/rhysd/dogfiles/blob/master/AutoHotkey.ahk

### Краткий FAQ по AutoHotkey

#### Как избежать проблем с отключением скриптов в приложениях, запущенных с правами админа

Допустим, мы залогинились в компьютер и запустили свой скрипт прямо с рабочего
стола. Клавиши перемапились и мы довольные идем работать. В процессе запускается
какое-то приложение (Visual Studio Installer), которое работает с админскими
правами. Замечаем интересную особенность: пока находимся в окне этого приложения
маппинг не работает совсем, как только переключаемся в предыдущее - все
возвращается на свои места.

Пути решения проблемы:

1. Щелкнуть правой кнопкой по этому скрипту и запустить с правами
   администратора. В результате все работает, но у нас две копии программы в
   памяти, одна отвечает за приложения с обычными правами, другая - с правами
   администратора. Если запускать только одну с правами администратора, то все
   начинает работать крайне нестабильно в приложениях с обычными правами: может
   работать, а может не работать, и вдруг потом - снова заработать. В моем
   случае иногда приходилось переключаться в окно администратора, там понажимать
   кнопки, чтобы потом те же кнопки заработали в обычных приложениях.

2. Скомпилировать скрипт в exe и сразу запускать с правами администратора. Та же
   проблема как и в предыдущем случае.

3. Решение состоит в подписи скомпилированного в п.2 файла и помещении его в
   каталог `C:\Program Files` или `C:\Windows\System32` и подробно описано
   [здесь](http://blog.danskingdom.com/get-autohotkey-to-interact-with-admin-windows-without-running-ahk-script-as-admin/)


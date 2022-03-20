# Ubuntu для web #

*\*Эта заметка атуальна для минимальной установки [Ubuntu 21.10](https://ubuntu.com/download)*

Я много раз сталкивался с необходимостью установки в Ubuntu ряда программ для веб-разработки. И каждый раз в разных
местах начинались танцы с бубном, поэтому я решил написать заметку, о том, как и в каком порядке обычно все
устанавливаю.

1. Я выбирал минимальную версию установки, поэтому стоит установить все доступные обновления:
    ```bash
    sudo apt update
    sudo apt upgrade
    ```
2. Нашел где-то в сети, что из коробки в Ubuntu поставляется несколько урезанная версия Firefox, которая не позволяет
   устанавливать расширения Gnome. Не знаю, насколько это правда, но знаю, что с программами, установленными через snap
   мне проблемы встречались чаще, чем с установленными через apt, поэтому сразу заменим Firefox:
   ```bash
   sudo snap remove --purge firefox
   sudo apt install firefox
   ```
3. Далее, если планируется использовать не стандартную командную оболочку, то необходимо выбрать и установить
   пропатченный шрифт. Сделать это можно на [Nerd Fonts](https://www.nerdfonts.com/font-downloads). Я остановил свой
   выбор на [Fira Code](https://github.com/ryanoasis/nerd-fonts/releases/download/v2.1.0/FiraCode.zip).  
   На заметку: при установке ttf начались глюки, с otf полет нормальный.
4. После установки шрифта можно поставить любимый терминал. Я выбираю Guake из-за quake мода и нормальной работы под
   Wayland (да, теперь в Ubuntu Wayland используется по дефолту, но можно запуститься и через Х11 - выбрать можно в окне
   входа в систему).
   ```bash
   sudo apt install guake
   sudo update-alternatives --install /usr/bin/x-terminal-emulator x-terminal-emulator /usr/bin/guake 50
   ```
   Второй строкой мы сделали так, чтобы наш терминал использовался в системе по умолчанию, но, к сожалению, это не
   распространяется на пункт контекстного меню Nautilus "Открыть в терминале" и я не нашел решения этой проблемы для
   актуальной версии.

   (опционально) Добавляем терминал в автозагрузку: Startup Application Preference -> Add и в открывшемся оконе в поле
   Command пишем `guake`, нажимаем Add.
5. Установим curl и git, т.к. они понадобятся в следующих шагах:
   ```bash
   sudo apt install curl git
   ```
6. Так как в терминале приходится проводить довольно много времени, то стоит сделать его более удобным и дружелюбным:
    1. Для начала установим zsh:
       `sudo apt install zsh`
    2. Сделаем его оболочкой по умолчанию: `chsh -s $(which zsh)`
    3. Теперь добавим oh-my-zsh:
       `sh -c "$(wget -O- https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"`
    4. И установим тему powerlevel10k:
        ```bash
        git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ~/powerlevel10k
        echo 'source ~/powerlevel10k/powerlevel10k.zsh-theme' >>~/.zshrc
        exec zsh
        p10k configure
        ```
    5. Добавим подсветку синтаксиса и автодополнение:
        ```bash
       git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
       git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
       git clone git://github.com/mbrubeck/compleat.git
       curl -sSL https://get.haskellstack.org/ | sh
       cd comleat
       make install
       cd ~
        ```
    6. Установим полезные утилиты:  
       `sudo apt install silversearcher-ag fasd fzf thefuck`
    7. (опционально) Установим GitHub-CLI:
        ```bash
       curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg
       echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null
       sudo apt update
       sudo apt install gh
        ```
    8. Донастроить zsh и p10k на свое усмотрение. Мои конфигурационные файлы вы можете найти в этом репозитории.
7. Установить минимально необходимый набор для разработки:
    1. Установим php:
        ```bash
       sudo apt install php
       ```
       Или, если нужна не дефолтная версия (например, 7.4):
       ```bash
       sudo add-apt-repository ppa:ondrej/php
       sudo apt update
       sudo apt install php7.4 php7.4-common php7.4-cli php7.4-{bz2,curl,intl,xml,mysql}
        ```
       Для переключения между версиями php используется:  
       `sudo update-alternatives --config php`
    2. Устанавливаем node (лучше всего через [nvm](https://github.com/nvm-sh/nvm#installing-and-updating), так как это
       позволяет удобно переключаться между версиями):
        ```bash
       curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash # текущая версия на момент написания
        ```  
       Установка node:  
       `nvm install 16`  
       Переключение между версиями:  
       `nvm use 16`
    3. Установка MySQL:  
        `sudo apt install mysql-server`
    4. Устанавливаем редактор кода или IDE на ваше усмотрение. Для меня это [PhpStorm](https://www.jetbrains.com/phpstorm/):  
       `sudo snap install phpstorm --calssic`
    5. (Опционально) Программы для скриншотов и скринкастов:
       `sudo apt install flameshot peek`  
       Можно к PrtSc привязать команду `flameshot gui`, а к Shift + PrtSc - `peek`

## Настройки и расширения GNOME (опционально) ##

```bash
# для любителей покопаться в настройках
sudo apt install gnome-tweaks
sudo apt install dconf-editor
# для быстрой настройки док-панели
gsettings set org.gnome.shell.extensions.dash-to-dock click-action 'minimize'
gsettings set org.gnome.shell.extensions.dash-to-dock extend-height false
gsettings set org.gnome.shell.extensions.dash-to-dock dock-position 'BOTTOM'
# расширения для GNOME
sudo apt install chrome-gnome-shell gnome-shell-extension-prefs gnome-shell-extension-appindicator gir1.2-appindicator3-0.1
```  

Вот список расширений, которые я обычно использую (помимо тех, что установлены выше):  
- [Extension List](https://extensions.gnome.org/extension/3088/extension-list/) - для управления остальными расширениями
- [Clipboard Indicator](https://extensions.gnome.org/extension/779/clipboard-indicator/) - довольно удобный менеджер буфера обмена. Мне его хватает, но если кому нужен более функциональный, то обратите внимание на [CopyQ](https://github.com/hluk/CopyQ)
- [Color Picker](https://extensions.gnome.org/extension/3396/color-picker/) - пипетка, работает с Wayland
- [Sound Input & Output Device Chooser](https://extensions.gnome.org/extension/906/sound-output-device-chooser/) - позволяет выбрать устройства для ввода и вывода звука
- [Desktop Icons NG (DING)](https://extensions.gnome.org/extension/2087/desktop-icons-ng-ding/) - помогает разобраться с папками на рабочем столе
- [Blur my Shell](https://extensions.gnome.org/extension/3193/blur-my-shell/) - для тех, кто хочет больше прозрачности

## Прочие программы (опционально) ##

На этом шаге можно устанавливать любые необходимые программы.  
Например, я обычно ставлю набор для работы с различными мультимедиа и мессенджер:
```bash
sudo apt install ubuntu-restricted-extras gnome-sushi gimp vlc telegram-desktop
```


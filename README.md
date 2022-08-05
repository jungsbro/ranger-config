# **ranger-config**
<br><br>

# **FolderTree**
```python
# ==============================================================================
.config/
    ranger/
        commands.py             # custom python scripts
        commands_full.py        #
        rc.conf                 # ranger config
        rifle.conf              # file-open config
        scope.sh                # file-preview config(755)
# ------------------------------------------------------------------------------
.git/
# ==============================================================================
README.md                       #
# ==============================================================================
```
---
<br><br>


# **Installation**
1. Install ranger
```bash
# ==============================================================================
caca-utils              # img2txt
highlight               # syntax highlight
atool                   # 7z
w3m                     # html page
poppler-utils           # pdf
mediainfo               # info about media file
python                  # json file
tar, p7zip-full         # preview archives
fzf                     # for searching
fasd                    # for searching
findutils               # for searching
mlocate                 # for searching
# ==============================================================================
mpv, kitty, terminology # image previews
imagemagick             # SVG preview
catimg                  # image-viewer in terminal (required ImageMagick )
ffmpegthumbnailer       # video thumbnails
calibre                 # ebook preview
transmission-show       # bitTorrent info
odt2txt                 # openDocument
fontimage               # font preview
openscad                # 3d model previews (stl,off,dxf,scad,csg)
# ==============================================================================
```

`# ubuntu, debian, raspberry`
```bash
sudo apt update;
sudo apt install -y ranger caca-utils highlight atool w3m poppler-utils mediainfo;
sudo apt install -y python tar p7zip-full trash-cli fzf fasd findutils mlocate;
```
```bash
sudo apt install -y mpv imagemagick catimg;
```

`# msys`
```bash
pacman -S python3 python3-pip
```
```bash
pip install ranger-fm
```
<br>


2. Clone ranger-config
```bash
git clone https://github.com/jungsbro/ranger-config.git ~/github/ranger-config
```
<br>


3. Copy ranger-config to home
```bash
cp -rf ~/github/ranger-config/.config ~;
chmod 755 ~/github/ranger-config/.config/ranger/scope.sh;
```

---
<br><br>


# **Settings**
> ranger 종료했을때, 마지막에 있었던 폴더 기억하기
```bash
vi ~/.bahsrc
```
```bash
alias ranger='ranger --choosedir=$HOME/.rangerdir; LASTDIR=`cat $HOME/.rangerdir`; cd "$LASTDIR"'
```
<br><br>


> ranger-config 새로 생성
```bash
ranger --copy-config=all
```
<br><br>


> user가 설정한 내용 root 사용
```bash
sudo ln -s ~/.config/ranger/ /root/.config/ranger/
```
<br><br>


> ranger 기본설정
```bash
vi ~/.config/ranger/rc.conf
```
```bash
# 외곽라인 보이기 ================================================================
set draw_borders true

# 숨겨진 파일 보기 ===============================================================
set show_hidden true

# tmux상태에서 sudo ragner 하면 error가 날때가 있다.( true → false ) ==============
set update_tmux_title false

# DELETE key 활성화 =============================================================
map <DELETE>   console delete

# custom key ( b, x, e ) 활용 ===================================================
map bc  cd /mnt/hdd1/config
map bd  cd /mnt/hdd1/download
map bD  cd /mnt/hdd1/torrent
map bg  cd /mnt/hdd1/game
map bG  cd /mnt/hdd1/emul
map bm  cd /mnt/hdd1/media
map bp  cd /mnt/hdd1/program
# map e
# map x
# ==============================================================================
```
<br><br>


> 이미지 프리뷰 가능(tui)
1. caca-utils 설치
```bash
sudo apt install caca-utils
```

2. 설정
`# 2-1. rc.conf`
```bash
vi ~/.config/ranger/rc.conf
```
```bash
# preview script를 사용 =========================================================
set preview_script ~/.config/ranger/scope.sh
set use_preview_script true

# 이미지 프리뷰 가능(tui) ========================================================
set preview_images false
set preview_images_method w3m

# 이미지 프리뷰 가능(gui) ========================================================
set preview_images true
set preview_images_method iterm2
# ==============================================================================
```

`# 2-2. scope.sh`
```bash
vi ~/.config/ranger/scope.sh
```
```bash
# 이미지 프리뷰 가능(tui) ========================================================
img2txt --gamma=0.6 --width=”${PV_WIDTH}” -- “%{FILE_PATH}” && exit 4
# ==============================================================================
```
<br><br>


> Delete key 활성화
1. trash-cli 설치
```bash
sudo apt install trash-cli
```

2. rc.conf 설정
```bash
vi ~/.config/ranger/rc.conf
```
```bash
# DELETE key 활성화 =============================================================
# map <DELETE>   console delete
map <DELETE>   shell -s trash-put %s
# ==============================================================================
```
<br><br>


> fzf in ranger
1. fzf 설치
```bash
sudo apt install ranger fzf findutils mlocate
```

2. commands.py 설정
```bash
vi ~/.config/ranger/commands.py
```
```bash
# fzf in ranger ================================================================
# https://github.com/ranger/ranger/wiki/Integrating-File-Search-with-fzf
# Now, simply bind this function to a key, by adding this to your ~/.config/ranger/rc.conf: map <C-f> fzf_select
class fzf_select(Command):
    """
    :fzf_select

    Find a file using fzf.

    With a prefix argument select only directories.

    See: https://github.com/junegunn/fzf
    """
    def execute(self):
        import subprocess
        if self.quantifier:
            # match only directories
            command="find -L . \( -path '*/\.*' -o -fstype 'dev' -o -fstype 'proc' \) -prune \
            -o -type d -print 2> /dev/null | sed 1d | cut -b3- | fzf +m"
        else:
            # match files and directories
            command="find -L . \( -path '*/\.*' -o -fstype 'dev' -o -fstype 'proc' \) -prune \
            -o -print 2> /dev/null | sed 1d | cut -b3- | fzf +m"
        fzf = self.fm.execute_command(command, stdout=subprocess.PIPE)
        stdout, stderr = fzf.communicate()
        if fzf.returncode == 0:
            fzf_file = os.path.abspath(stdout.decode('utf-8').rstrip('\n'))
            if os.path.isdir(fzf_file):
                self.fm.cd(fzf_file)
            else:
                self.fm.select_file(fzf_file)
# ------------------------------------------------------------------------------

class fzf_locate(Command):
    """
    :fzf_locate

    Find a file using fzf.

    With a prefix argument select only directories.

    See: https://github.com/junegunn/fzf
    """
    def execute(self):
        import subprocess
        if self.quantifier:
            command="locate home media | fzf -e -i"
        else:
            command="locate home media | fzf -e -i"
        fzf = self.fm.execute_command(command, stdout=subprocess.PIPE)
        stdout, stderr = fzf.communicate()
        if fzf.returncode == 0:
            fzf_file = os.path.abspath(stdout.decode('utf-8').rstrip('\n'))
            if os.path.isdir(fzf_file):
                self.fm.cd(fzf_file)
            else:
                self.fm.select_file(fzf_file)
# ------------------------------------------------------------------------------
# ==============================================================================
```

3. rc.conf 설정
```bash
vi ~/.config/ranger/rc.conf
```
```bash
# map <C-f> fzf_select
# map <C-g> fzf_locate
# map zd    fzf_select
# map zl    fzf_locate
map <C-t>   fzf_select
map zl    fzf_locate
```

4. updatedb.conf 수정
`# locate command database에 /media를 포함시키기 위해`
```bash
sudo vi /etc/updatedb.conf;
```
`# PRUNEPATHS에서 /media 삭제 후`
```bash
# PRUNEPATHS="/tmp /var/spool /media /var/lib/os-prober /var/lib/ceph"
PRUNEPATHS="/tmp /var/spool /var/lib/os-prober /var/lib/ceph"
```
`# updatedb 실행`
```bash
sudo updatedb;
```
<br><br>


> fzf and fasd in ranger
1. fzf, fasd 설치
```bash
sudo apt install ranger fzf fasd
```

2. commands.py 설정
```bash
vi ~/.config/ranger/commands.py
```
```bash
# fzf and fasd in ranger =======================================================
# fzf_fasd - Fasd + Fzf + Ranger (Interactive Style) ---------------------------
class fzf_fasd(Command):
    """
    :fzf_fasd

    Jump to a file or folder using Fasd and fzf

    URL: https://github.com/clvv/fasd
    URL: https://github.com/junegunn/fzf
    """
    def execute(self):
        import subprocess
        if self.quantifier:
            command="fasd | fzf -e -i --tac --no-sort | awk '{ print substr($0, index($0,$2)) }'"
        else:
            command="fasd | fzf -e -i --tac --no-sort | awk '{ print substr($0, index($0,$2)) }'"
        fzf = self.fm.execute_command(command, stdout=subprocess.PIPE)
        stdout, stderr = fzf.communicate()
        if fzf.returncode == 0:
            fzf_file = os.path.abspath(stdout.decode('utf-8').rstrip('\n'))
            if os.path.isdir(fzf_file):
                self.fm.cd(fzf_file)
            else:
                self.fm.select_file(fzf_file)
# ------------------------------------------------------------------------------

# Fasd with ranger (Command Line Style) ----------------------------------------
# https://github.com/ranger/ranger/wiki/Commands
class fasd(Command):
    """
    :fasd

    Jump to directory using fasd
    URL: https://github.com/clvv/fasd
    """
    def execute(self):
        import subprocess
        arg = self.rest(1)
        if arg:
            directory = subprocess.check_output(["fasd", "-d"]+arg.split(), universal_newlines=True).strip()
            self.fm.cd(directory)
# ------------------------------------------------------------------------------
# ==============================================================================
```

3. rc.conf 설정
```bash
vi ~/.config/ranger/rc.conf
```
```bash
map zz   fzf_fasd
map zo   console fasd%space
```
<br><br>


---
<br><br>
# **ranger-config**
<br>

# **FolderTree**
```python
# ==============================================================================
.config/
    ranger/
        commands.py             #
        commands_full.py        #
        rc.conf                 #
        rifle.conf              #
        scope.sh                #
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

1. Install ranger
- ubuntu, debian, raspberry
```bash
sudo apt install -y ranger caca-utils highlight atool w3m poppler-utils mediainfo python tar p7zip-full trash-cli fzf fasd findutils mlocate;
```
```bash
sudo apt install -y mpv imagemagick catimg;
```

- msys
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
cp -rf ~/github/ranger-config/.config ~
```
<br>


---
<br><br>

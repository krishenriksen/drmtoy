### Get drmtoy enum and drmsend
```
git clone -b wip-drm-send-recv https://github.com/krishenriksen/drmtoy.git
cd drmtoy
make enum drmsend
```

### Find the right framebuffer id for your screen
`./build/rel/enum`

### It will output lots of lines. You're interested in the last few lines that will looks something like these:
```
count_fbs = 1
	0: 0x49
		width=320 height=480 pitch=640 bpp=16 depth=16 handle=0
```

### Run drmsend with elevated privileges. Replace 0x49 with your framebuffer id
`sudo ./build/rel/drmsend 0x49 drmsend.sock &`

### chown socket so that your user can access it
`USER=$(whoami) sudo chown $USER drmsend.sock`

### Alternatively you can set the right caps on drmsend and run it under a regular user. Note that you'd need your fs to be mounted without nosuid flag.
```
sudo setcap cap_sys_admin+ep ./build/rel/drmsend
sudo chown root ./build/rel/drmsend
./build/rel/drmsend 0x49 drmsend.sock &
```

### Get patched OBS
```
git clone https://github.com/w23/obs-studio.git
cd obs-studio
git checkout linux-libdrm-grab
```

### Build patched OBS
```
mkdir build
cd build
cmake .. -DUNIX_STRUCTURE=0 -DUSE_EGL=1 -GNinja
ninja
```

### Run it.
```
cd rundir/RelWithDebInfo/bin/64bit/
./obs -p
```

### Now you can add "DMA-BUF source" to your scene as you would with any other regular source.
In configuration dialog you need to specify drmsend socket:

    Click "Browse" and navigate to the directory with drmsend.sock, e.g. where you checked out drmtoy and ran build/rel/drmsend
    QFileDialog will be unhelpful enough to not show UNIX sockets, so you'd need to type your filename manually, e.g. drmsend.sock and press enter.
    Now you should have a preview of your framebuffer screen. Note that there's no cursor capture yet.
    VYGODA

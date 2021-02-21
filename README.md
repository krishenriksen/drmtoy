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

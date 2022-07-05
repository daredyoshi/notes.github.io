# How to build vim with ycm completer on centos with no sudo access

You will need to grab vim from source
[vim]( !https://github.com/vim/vim )

You will need to grab Ycm from the legacy cpp 11 branch 
[ycm](https://github.com/ycm-core/YouCompleteMe/tree/legacy-c+%2B11)

You will need to get python 3.6 from source (official release didn't work for me)
[pyton](https://github.com/python/cpython/tree/3.6)

First you will need to compile python with shared libs so Ycm can link against it later.

```bash
# Go to where you cloned python
./configure --prefix=/path/to/usr/.local/python3 --enable-optimizations --enable-shared
make -j16
make -j16 install
```

Then you need to compile vim with shared libs. This is a little more complicated

```bash
export LDFLAGS="-rdynamic"
export LD_LIBRARY_PATH="/path/to/usr/.local/python3/lib"
export PATH={$PATH}:/path/to/usr/.local/python3/bin"

# cd to vim source dir
./configure --with-featuers=huge --enable-multibyte --enable-python3interp=yes --with-python3-command=/path/to/usr/.local/python3/bin/python3 --enable-cscope --prefix=/path/to/usr/.local/vim/ --enable-fail-if-missing

# destdir and vimruntime dir must be set
make -j16 VIMRUNTIMEDIR=/path/to/usr/.local/share/vim DESTDIR=/path/to/usr/.local/vim
make -j16 install VIMRUNTIMEDIR=/path/to/usr/.local/share/vim DESTDIR=/path/to/usr/.local/vim
"
```

Then you need to compile ycm for ycm you will need to download cmake 3 and modify the python path. To modify the python path I just manually overwrote it
To download ycm if you are behind a nomachine internet or similar I reccomend cloning it on your home machine to get all the git submodules and then emailing a zip of that to yourself. 

```
vim /path/to/usr/.vim/bundle/YouCompleteMe/third_party/ycmd/build.py
```

Search for include_dir = 
On line 295 change it to this

```python
include_dir = '/path/to/usr/.local/python3/include/python3.6';
```

Then in the ycm root directory

```bash
cd /path/to/usr/.vim/bundle/YouCompleteMe/
/path/to/usr/.local/python3/bin/python3 install.py --cmake-path /path/to/usr/.local/cmake/cmake
```

Then everything should wrk!





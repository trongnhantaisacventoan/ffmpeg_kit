git clone https://github.com/AliAkhgar/ffmpeg-kit-16KB.git

export ANDROID_SDK_ROOT=/Users/nhannguyentrong/Library/Android/sdk
export ANDROID_NDK_ROOT=/Users/nhannguyentrong/Library/Android/sdk/ndk/25.1.8937393

./android.sh -d --full --enable-gpl --disable-arm-v7a --disable-gnutls
./android.sh -d --full --enable-gpl --disable-lib-gnutls --disable-arm-v7a

# other error

https://github.com/arthenica/ffmpeg-kit/wiki/Troubleshooting

# fix permission

sudo chown -R $(whoami):admin /usr/local/lib /usr/local/include /usr/local/share

brew install autoconf automake libtool pkg-config curl git doxygen nasm cmake gcc gperf texinfo yasm bison autogen wget gettext meson ninja ragel groff gtk-doc libtasn1

## fix x265 at cmakelist -> comment line 10 -> line 17

```
message(STATUS "cmake version ${CMAKE_VERSION}")
# if(POLICY CMP0025)
#     cmake_policy(SET CMP0025 OLD) # report Apple's Clang as just Clang
# endif()
# if(POLICY CMP0042)
    cmake_policy(SET CMP0042 NEW) # MACOSX_RPATH
# endif()
# if(POLICY CMP0054)
#     cmake_policy(SET CMP0054 OLD) # Only interpret if() arguments as variables or keywords when unquoted
# endif()
```

## fix libvidstab,snappy,soxr,chromaprint,srt,jpeg cmakelist

```
cmake_minimum_required (VERSION 2.8.5) => cmake_minimum_required (VERSION 3.5)
```

## gnutls

. required bison version > 2.4
. on mac sometime use wrong bison 2.3

fix by make sure bison from homebrew is used. run command before build

```
export PATH="$(brew --prefix bison)/bin:$PATH"
```

# ffmpeg.sh

main-android.sh line 201 -> 204 => completed not increase so loop

```
else
((completed += 1))
declare "$BUILD_COMPLETED_FLAG=1"
      echo -e "INFO: Skipping $library, dependencies built=$run, already built=${!BUILD_COMPLETED_FLAG}\n" 1>>"${BASEDIR}"/build.log 2>&1
fi

```

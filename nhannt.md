# step build

tail -f -n 100 build.log

. remove -d if not build debug

```
git clone https://github.com/AliAkhgar/ffmpeg-kit-16KB.git
export ANDROID_SDK_ROOT=/Users/nhannguyentrong/Library/Android/sdk
export ANDROID_NDK_ROOT=/Users/nhannguyentrong/Library/Android/sdk/ndk/25.1.8937393

 ./android.sh --enable-android-media-codec --enable-android-zlib --enable-xvidcore --enable-libvidstab --enable-x264 --enable-x265 --disable-arm-v7a

./android.sh -d --full --enable-gpl --disable-arm-v7a

./android.sh -d --enable-android-media-codec --enable-android-zlib

# build full -> openssl not support x86 so disabled it
./android.sh -d --full --enable-gpl --disable-arm-v7a --disable-lib-srt --disable-lib-openssl
```

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

### GnuTLS and OpenSSL must not be enabled at the same time.

# ffmpeg.sh

REMOVE

```
 # gnutls)
    #   CFLAGS+=" $(pkg-config --cflags gnutls 2>>"${BASEDIR}"/build.log)"
    #   LDFLAGS+=" $(pkg-config --libs --static gnutls 2>>"${BASEDIR}"/build.log)"
    #   CONFIGURE_POSTFIX+=" --enable-gnutls"
    #   ;;

```

## PUBLISH MAVEN

1. replace xxx with passpharse of gpg

```
signing.gnupg.passphrase=xxx
```

2. run build local

brew install gpg

```
gpg --full-generate-key
(1) RSA and RSA

4096

0

gpg --list-secret-keys --keyid-format LONG

gpg --armor --export FF8C15F9EEAC7D49
copy roi submit
https://keyserver.ubuntu.com/#

```

```
repositories {
        maven {
            name = "ffmpeg"
            url = uri("../../../local-repo") // Path to your local Maven repository
        }
    }

```

3. go to local-repo zip folder "io"
4. login https://central.sonatype.com/publishing => login use github account is trongnhan136@gmail.com not use gmail account
5. upload zip to deployment

---

# ios

Building arm64 platform targeting iOS SDK 12.1 and Mac Catalyst 14.0

goi cai nay de bo qua build lau

```
export SKIP_ffmpeg=1
export SKIP_ffmpeg_kit=1
```

./ios.sh --xcframework --enable-ios-audiotoolbox --enable-ios-avfoundation --enable-ios-bzip2 --enable-ios-libiconv --enable-ios-videotoolbox --enable-ios-zlib

# build full have error with gnutls

./ios.sh --xcframework --full --enable-gpl --disable-lib-srt --disable-lib-openssl

1. "iconv.m4" not found by auto config. so need specific folder by command. change folder base on machine
   -> problem is build LAME error
   auto config variable is ACLOCAL_PATH

```
export ACLOCAL_PATH="/usr/local/Cellar/gettext/0.26_1/share/gettext/m4:/usr/local/share/aclocal"
```

2. fix cmake like android above
   for x265 => tools/patch/cmake/x265/ change cmakelist here

3. libpng => sources.sh change to 1.6.50

```
libpng)
    SOURCE_REPO_URL="https://github.com/arthenica/libpng"
    SOURCE_ID="v1.6.50"
    SOURCE_TYPE="TAG"
```

4. gnutls
   comment if got error relate -march=all
   line 13219

```
# # Check if the assembler supports -march=all
# if test "$hw_accel" = aarch64; then
#   AARCH64_CCASFLAGS="-Wa,-march=all"
#   { printf "%s\n" "$as_me:${as_lineno-$LINENO}: checking whether the compiler supports -Wa,-march=all" >&5
# printf %s "checking whether the compiler supports -Wa,-march=all... " >&6; }
#   : > conftest.s
#   if "$CCAS" "$AARCH64_CCASFLAGS" -c conftest.s >/dev/null 2>&1; then
#     { printf "%s\n" "$as_me:${as_lineno-$LINENO}: result: yes" >&5
# printf "%s\n" "yes" >&6; }
#   else
#     { printf "%s\n" "$as_me:${as_lineno-$LINENO}: result: no" >&5
# printf "%s\n" "no" >&6; }
#     AARCH64_CCASFLAGS=
#   fi

# fi

```

5. leptonica
   just remove giflib and run again

6. x265.sh

them dieu kien

```
x86_64)
  # iOS simulator
  ASM_OPTIONS="-DENABLE_ASSEMBLY=0 -DCROSS_COMPILE_ARM=0"
  ;;

```

```

# SET BUILD OPTIONS
case ${ARCH} in
armv7 | armv7s)
  ASM_OPTIONS="-DENABLE_ASSEMBLY=1 -DCROSS_COMPILE_ARM=1"
  ;;
arm64*)
  ASM_OPTIONS="-DENABLE_ASSEMBLY=0 -DCROSS_COMPILE_ARM=1"
  ;;
x86_64)
  # iOS simulator
  ASM_OPTIONS="-DENABLE_ASSEMBLY=0 -DCROSS_COMPILE_ARM=0"
  ;;
x86-64-mac-catalyst)
  ASM_OPTIONS="-DENABLE_ASSEMBLY=0 -DCROSS_COMPILE_ARM=0"
  ;;
i386)
  ASM_OPTIONS="-DENABLE_ASSEMBLY=0 -DCROSS_COMPILE_ARM=0"
  ;;
*)
  ASM_OPTIONS="-DENABLE_ASSEMBLY=1 -DCROSS_COMPILE_ARM=0"
  ;;
esac
```

7. liencese error

replace this method

```
# 1 - library index
get_external_library_license_path() {
  case $1 in
  1) echo "${BASEDIR}/src/$(get_library_name "$1")/LICENSE.TXT" ;;
  12) echo "${BASEDIR}/src/$(get_library_name "$1")/Copyright" ;;
  35) echo "${BASEDIR}/src/$(get_library_name "$1")/LICENSE.txt" ;;
  3 | 42) echo "${BASEDIR}/src/$(get_library_name "$1")/COPYING.LESSERv3" ;;
  5 | 44) echo "${BASEDIR}/src/$(get_library_name "$1")/$(get_library_name "$1")/COPYING" ;;
  19) echo "${BASEDIR}/src/$(get_library_name "$1")/$(get_library_name "$1")/LICENSE" ;;
  26) echo "${BASEDIR}/src/$(get_library_name "$1")/COPYING.LGPL" ;;
  28 | 38) echo "${BASEDIR}/src/$(get_library_name "$1")/LICENSE.md" ;;
  30) echo "${BASEDIR}/src/$(get_library_name "$1")/COPYING.txt" ;;
  43) echo "${BASEDIR}/src/$(get_library_name "$1")/COPYRIGHT" ;;
  46) echo "${BASEDIR}/src/$(get_library_name "$1")/leptonica-license.txt" ;;
  10 | 13 | 17 | 21 | 27 | 31 | 32 | 36 | 40 | 49) echo "${BASEDIR}/src/$(get_library_name "$1")/LICENSE" ;;
  *) echo "${BASEDIR}/src/$(get_library_name "$1")/COPYING" ;;
  esac
}
```

## build full in my drive google

# OpenImageIO

OpenImageIO는 VFX, 게임, 애니메이션 작업등 제작 파이프라인에서 사용할 수 있는 이미지 컨버팅 도구, API 입니다.
ACES(OpenColorIO)를 지원합니다.

이미지를 컨버팅할 때 사용합니다.
오픈소스로 파이프라인을 구축할 때 굉장히 파워풀한 도구가 됩니다.

### 지원하는 포멧
TIFF, JPEG/JFIF, OpenEXR, PNG, HDR/RGBE, ICO, BMP, Targa, JPEG-2000,
RMan Zfile, FITS, DDS, Softimage PIC, PNM, DPX, Cineon, IFF, Field3D,
OpenVDB, Ptex, Photoshop PSD, Wavefront RLA, SGI, WebP, GIF, DICOM,
많은 디지털카메라의 Raw포멧 등

## 설치
낮은 버전은 CentOS7.9 에서 아래처럼 손쉽게 설치할 수 있습니다.
높은 버전을 사용하기 위해서는 직접 컴파일이 필요합니다.

리눅스
```bash
$ sudo yum install OpenImageIO
$ sudo yum install OpenImageIO-iv
$ sudo yum install OpenImageIO-devel
$ sudo yum install OpenImageIO-utils
$ sudo yum install python-OpenImageIO
```

macOS
```bash
$ brew install openimageio
```

- Windows
	- https://github.com/OpenImageIO/oiio/blob/master/INSTALL.md
	- https://sites.google.com/site/openimageio/building-oiio-on-windows

## 명령어
OpenImageIO, OpenImageIO Util 명령어를 알아보겠습니다.

### iinfo
이미지의 정보를 분석하는 툴입니다.

```bash
$ iinfo test.exr 
test.exr : 2880 x 1620, 3 channel, half openexr
```

### iconvert
이미지를 컨버팅할 때 사용합니다.
일반적으로 exr파일에 메타데이터를 추가할 때 더 많이 사용하는 명령어 입니다.

```bash
$ iconvert --inplace --caption testimage test.exr
```

이미지에 키워드 추가하기
```bash
$ iconvert --inplace --keyword woong test.exr
```

exr이미지에 어트리뷰트 추가하기
```bash
$ iconvert --inplace --attrib project circle --attrib shot FOO_0010 test.exr
```

### idiff
이미지를 비교하는 명령어

PASS 문자가 출력되면 같은 이미지 입니다.
```bash
$ idiff image1.exr image2.exr 
Comparing "image1.exr" and "image2.exr"
PASS
```

결과가 다르면 분석값을 출력하고 FAILURE을 최종 출력합니다.
```bash
$ idiff image1.exr image2.exr
Comparing "image1.exr" and "image2.exr"
  Mean error = 0.327311
  RMS error = 0.508405
  Peak SNR = 28.74
  Max error  = 13.0548 @ (843, 275, B)
  4665600 pixels (100%) over 1e-06
  4665600 pixels (100%) over 1e-06
FAILURE
```

### igrep
이미지 메타데이터를 검색할 때 사용합니다.

만약 이미지 내부에 `A004R23J` 문자가 존재하는지 검사할 때

```bash
$ igrep A004R23J test.exr
```

### iv
이미지 뷰어입니다.

![iv](../figures/iv.png)

```bash
$ iv input.ext
```

### maketx
[minmap](https://ko.wikipedia.org/wiki/밉맵) tx 파일이 생성됩니다.(작은이미지를 미리 만들어 놓는 과정. -> 렌더링 속도 향상을 목적으로 합니다.)
이미지를 MipMap 타일 이미지로 변환하는 명령어 입니다. Pixar Renderman의 txmake와 비슷한 명령어입니다.
minmap과 비슷한 개념은 모델링 데이터에서 LOD(Level of Detail) 개념과 비슷합니다.(텍스쳐 LOD죠!)

소니이미지웍스의 Larry Gritz에 의해서 개발되었습니다.

> 참고 : 픽사의 렌더맨을 설치하면 내부에 있는 txmake 명령어와 혼동하기 쉽습니다.

```bash
$ maketx input.jpg
input.tx 파일이 생성됩니다.
```

oiio 타일사이즈 기준으로 변경.
```bash
$ maketx -v -u --oiio --checknan --filter lanczos3 path/to/fileIn.tif -o path/to/fileOut.tx
```

렌더맨 타일사이즈 기준으로 변경.
```bash
$ maketx -v -u --prman --checknan --filter lanczos3 path/to/fileIn.tif -o path/to/fileOut.tx
```

#### Reference
- https://docs.arnoldrenderer.com/display/A5AFMUG/Tx+Manager
- https://docs.arnoldrenderer.com/display/A5AFMUG/Maketx
- https://answers.unity.com/questions/310352/texture-mipmap-distance.html

### oiiotool
OpenImageIO를 설치하면 사용할 수 있는 이미지 프로세싱 툴입니다.
가장 많이 사용하게 될 명령어 입니다.

## 컬러 프로파일 로딩 체크
[OpenColorIO-Configs 설치방법](opencolorio.md)
```bash
$ export OCIO=$HOME/app/OpenColorIO-Configs/aces_1.0.3/config.ocio
```

OCIO를 인식하는지 체크해보겠습니다.
```bash
$ oiiotool --help
```

OpenImageIO 2.1.0 버전처럼 높은 버전에서는 Colorspace 리스트를 보기위해 아래 명령어처럼 입력이 필요합니다.
```bash
$ oiiotool --colorconfig
```

아래처럼 컬러스페이스 리스트가 나오면 정상입니다.
```bash
Known color spaces:

"ACES - ACES2065-1",
"ACES - ACEScc",
"ACES - ACEScct",
"ACES - ACESproxy",
"ACES - ACEScg" (linear),
....
"role_matte_paint",
"role_color_picking",
"role_color_timing"

Known displays:
	"ACES"* (views:
			"sRGB"*,
			"DCDM",
			"DCDM P3 gamut clip",
			"P3-D60",
			"P3-D60 ST2084 1000 nits",
			"P3-D60 ST2084 2000 nits",
			"P3-D60 ST2084 4000 nits",
			"P3-DCI",
			"Rec.2020",
			"Rec.2020 ST2084 1000 nits",
			"Rec.709",
			"Rec.709 D60 sim.",
			"sRGB D60 sim.",
			"Raw",
			"Log"
		) (* = default)
```

oiiotool을 사용할 준비가 끝났습니다.

## ACES exr to rec709
oiiotool을 가장 많이 사용할 때는 ACES exr 파일을 아티스트가 보기 편한 rec709 컬러스페이스 프리뷰 이미지로 변환하는 일입니다. 변환해 보겠습니다.

테스팅 할 것

```bash
$ oiiotool input.exr --colorconvert "ACES - ACEScg" "Output - Rec.709" -o ouput.jpg
```

프리뷰 이미지를 만들 때 --fit 옵션을 사용하면 리사이즈 할 수 있습니다.

```bash
$ oiiotool input.exr --colorconvert "ACES - ACEScg" "Output - Rec.709" --fit 320x240 -o ouput.jpg
```
## Dpx to sRGB
참고 : ADX10은 ACES DPX 10bit 의 약자입니다.

```bash
$ oiiotool input.dpx --colorconvert "Input - ADX - ADX10" "Output - sRGB" -o ouput.jpg
```

만약 dpx가 Arri V3 LogC 커브로 인코딩 되어있다면 아래같은 옵션을 사용할 수 있습니다.
```bash
$ oiiotool input.dpx --colorconvert "Input - ARRI - Curve - V3 LogC (EI800)" "Output - sRGB" -o ouput.jpg
```

## .exr to .tga
일반적으로 .exr 이미지는 linear 컬러스페이스를 가지며, tga 파일은 sRGB 컬러스페이스를 가집니다.
oiiotool 명령어는 기본적으로 이미지 알파 채널에 대해서 premult를 하지 않으니 알파가 있는 exr 이미지 컨버팅시에는 꼭 `--premult` 옵션을 달아주세요.

```bash
$ oiiotool input.exr --colorconvert linear srgb --premult -o output.tga
```

## 이미지 리사이즈 
이미지를 리사이즈 할 때는 `--resize` 옵션을 사용할 수 있습니다.
```bash
$ oiiotool input.exr --resize 2048x1152 -o output.exr
```

## Timecode 확인
exr 내부에 들어있는 타임코드를 확인할 수 있습니다.
```bash
$ oiiotool --info -v input.exr
Reading input.exr
input.exr : 4096 x 2160, 3 channel, half openexr
channel list: R, G, B
oiio:ColorSpace: "Linear"
compression: "none"
PixelAspectRatio: 1
screenWindowCenter: 0 0
screenWindowWidth: 1
smpte:TimeCode: 01:18:19:06
```

## 컴파일
위에서 필요한 명령어는 간단하게 설치가 끝났습니다.
명령어를 위해서 컴파일 할 필요는 없지만, 다른 프로그램을 컴파일할 때 활용됩니다.

```bash
$ sudo yum install clang
$ sudo yum install webp-devel
$ sudo yum install LibRaw-devel
$ sudo yum install opencv-devel
$ sudo yum install libtiff-devel
```

## OpenColorIO Core 설치
OpenImageIO를 컴파일 하기 위해서는 먼저 OpenColorIO Core 가 필요합니다. app에 설치해주세요.

```bash
$ cd ~/app
$ wget https://github.com/AcademySoftwareFoundation/OpenColorIO/archive/refs/tags/v2.0.1.tar.gz
$ tar -zxvf v2.0.1.tar.gz
$ rm v2.0.1.tar.gz
```

## IlmBase 컴파일, OpenEXR 컴파일
- [문서 바로가기](openexr.md)

## Boost 컴파일
- [boost 컴파일](boost.md)

## OpenImageIO 컴파일
아래 명령어를 실행하면 일단 빌드가 됩니다.
oiio와 함께 연동이 필요한 라이브러리는 필요시 추가하여 빌드합니다.

```bash
$ cd ~/app
$ git clone https://github.com/OpenImageIO/oiio OpenImageIO_src
$ mkdir OpenImageIO
$ cd OpenImageIO_src
$ scl enable devtoolset-9 bash
$ export PATH=$PATH:$HOME/app/cmake-3.20.5/bin #cmake가 필요합니다. PATH에 잡아줍니다.
$ # boost가 필요합니다. 컴파일 합니다.
$ sh src/build-scripts/build_openexr.bash
$ sh src/build-scripts/build_OpenJPEG.bash # .jpg
$ sh src/build-scripts/build_libjpeg-turbo.bash # .jpg 지원
$ sh src/build-scripts/build_libpng.bash # .png 지원
$ sh src/build-scripts/build_libtiff.bash # .tiff 지원
$ make VERBOSE=1 OpenEXR_ROOT=${PWD}/src/build-scripts/ext/dist ILMBASE_HOME=$HOME/app/IlmBase OCIO_HOME=$HOME/app/OpenColorIO-2.0.1 STOP_ON_WARNING=0 USE_OCIO=1 INSTALL_PREFIX=$HOME/app/OpenImageIO Boost_ROOT=$HOME/app/boost_1_73_0 JPEG_ROOT=${PWD}/src/build-scripts/ext/dist JPEGTurbo_ROOT=${PWD}/src/build-scripts/ext/dist PNG_ROOT=${PWD}/src/build-scripts/ext/dist LIBTIFF_ROOT=${PWD}/src/build-scripts/ext/dist USE_PYTHON=0
$ make install
```

잘 컴파일이 되었는지 체크하기 위해 oiiotool 명령어 실행하기
```bash
$ export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/dotori/app/openexr-2.5.7/lib:/home/dotori/app/IlmBase:/home/dotori/app/OpenImageIO_src/src/build-scripts/ext/dist/lib:/home/dotori/app/OpenImageIO_src/src/build-scripts/ext/dist/lib:/home/dotori/app/OpenImageIO/lib64:/home/dotori/app/OpenImageIO/lib64:/home/dotori/app/OpenImageIO_src/src/build-scripts/ext/dist/lib64 # oiiotool을 실행하기 위해서 필요한 .so 파일을 로딩합니다.
$ export OCIO=$HOME/app/OpenColorIO-Configs/aces_1.0.3/config.ocio # 이미지 연산을 위해 OpenColorIO를 설정합니다.
$ ./oiiotool
```

## 실 습
- OpenImageIO를 컴파일 합니다.
- Python과 oiiotool을 이용해서 썸네일을 만들어봅시다.
- 각 옵션들을 다르게 설정해서 실행해 봅니다.

## Reference
- https://github.com/OpenImageIO/oiio/blob/master/src/doc/openimageio.pdf
- [webp 란?](https://ko.wikipedia.org/wiki/WebP) : jpeg를 대체하기 위해 구글에서 개발된 포멧입니다. 사파리에서 보이지 않습니다.

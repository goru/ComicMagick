FROM emscripten/emsdk

RUN git clone https://github.com/ImageMagick/jpeg-turbo.git libjpeg
#RUN git clone https://github.com/mozilla/mozjpeg.git libjpeg && cd libjpeg && git checkout v4.0.3
RUN git clone https://github.com/ImageMagick/ImageMagick.git && cd ImageMagick && git checkout 7.0.11-4

ARG LIBJPEG_CFLAGS=""
ARG LIBJPEG_LDFLAGS="-O3 --closure 1"
ARG LIBJPEG_PREFIX="/opt/libjpeg"
ARG LIBJPEG_FLAGS="-DCMAKE_INSTALL_PREFIX=$LIBJPEG_PREFIX -DENABLE_SHARED=0 -DWITH_SIMD=0"
#ARG LIBJPEG_FLAGS="-DCMAKE_INSTALL_PREFIX=$LIBJPEG_PREFIX -DENABLE_SHARED=0 -DWITH_SIMD=0 -DPNG_SUPPORTED=0"
ENV CFLAGS="$LIBJPEG_CFLAGS"
ENV LDFLAGS="$LIBJPEG_LDFLAGS"
RUN cd libjpeg &&\
  emcmake cmake -G"Unix Makefiles" $LIBJPEG_FLAGS . &&\
  emmake make -j$(grep processor /proc/cpuinfo | wc -l) &&\
  emmake make install

ARG IMAGE_MAGICK_CFLAGS="-O3"
ARG IMAGE_MAGICK_LDFLAGS="-O3 --closure 1"
ARG IMAGE_MAGICK_FLAGS="--disable-shared --disable-hdri --disable-docs --without-threads --without-magick-plus-plus"
ENV CFLAGS="-I$LIBJPEG_PREFIX/include/ $IMAGE_MAGICK_CFLAGS"
ENV CXXFLAGS="$CFLAGS"
ENV CPPFLAGS="-I$LIBJPEG_PREFIX/include/"
ENV LDFLAGS="-L$LIBJPEG_PREFIX/lib/ $IMAGE_MAGICK_LDFLAGS"
RUN cd ImageMagick &&\
  emconfigure ./configure $IMAGE_MAGICK_FLAGS &&\
  sed -i 's/^\(EXEEXT =\).*/\1 .html/' Makefile &&\
  emmake make -j$(grep processor /proc/cpuinfo | wc -l)

# https://github.com/emscripten-core/emscripten/blob/main/src/settings.js
ARG EMCC_LDFLAGS="-s INVOKE_RUN=0 -s MODULARIZE=1 -s EXPORT_NAME=Magick -s EXTRA_EXPORTED_RUNTIME_METHODS=['callMain']"
ENV LDFLAGS="$IMAGE_MAGICK_LDFLAGS $EMCC_LDFLAGS"
RUN cd ImageMagick &&\
  mkdir wasm &&\
  ./libtool --silent --tag=CC --mode=link emcc $LDFLAGS -o wasm/magick.js utilities/magick.o MagickCore/libMagickCore-*.la MagickWand/libMagickWand-*.la

#RUN apt update && apt install -y file less vim cmake-curses-gui

CMD (cd ImageMagick/utilities; python3 -m http.server 80)

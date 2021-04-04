FROM emscripten/emsdk

#RUN apt update && apt install -y file less vim

RUN git clone https://github.com/ImageMagick/jpeg-turbo.git
RUN git clone https://github.com/ImageMagick/ImageMagick.git && cd ImageMagick && git checkout 7.0.11-4

ARG MAKE_JOBS

ARG JPEG_CFLAGS=""
ARG JPEG_LDFLAGS="-O3 --closure 1"
ENV CFLAGS="$JPEG_CFLAGS"
ENV LDFLAGS="$JPEG_LDFLAGS"
RUN cd jpeg-turbo && emcmake cmake -G"Unix Makefiles" -DENABLE_SHARED=0 -DWITH_SIMD=0 . && emmake make $MAKE_JOBS && emmake make install

ARG IMAGE_MAGICK_CFLAGS="-O3"
ARG IMAGE_MAGICK_LDFLAGS="-O3 --closure 1"
ENV CFLAGS="-I/opt/libjpeg-turbo/include/ $IMAGE_MAGICK_CFLAGS"
ENV CXXFLAGS="$CFLAGS"
ENV CPPFLAGS="-I/opt/libjpeg-turbo/include/"
ENV LDFLAGS="-L/opt/libjpeg-turbo/lib32/ $IMAGE_MAGICK_LDFLAGS"
RUN cd ImageMagick && emconfigure ./configure --disable-shared --disable-hdri --disable-docs --without-threads --without-magick-plus-plus && sed -i 's/^\(EXEEXT =\).*/\1 .html/' Makefile && emmake make $MAKE_JOBS

# https://github.com/emscripten-core/emscripten/blob/main/src/settings.js
ARG EMCC_LDFLAGS="-s INVOKE_RUN=0 -s MODULARIZE=1 -s EXPORT_NAME=Magick -s EXTRA_EXPORTED_RUNTIME_METHODS=['callMain']"
ENV LDFLAGS="$IMAGE_MAGICK_LDFLAGS $EMCC_LDFLAGS"
RUN cd ImageMagick && mkdir wasm && ./libtool --silent --tag=CC --mode=link emcc $LDFLAGS -o wasm/magick.js utilities/magick.o MagickCore/libMagickCore-*.la MagickWand/libMagickWand-*.la

ENTRYPOINT (cd ImageMagick/utilities; python3 -m http.server 80)

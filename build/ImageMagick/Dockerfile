FROM emscripten/emsdk

ARG MAKE_JOBS

#RUN apt update && apt install -y file less vim

RUN git clone https://github.com/ImageMagick/jpeg-turbo.git
RUN git clone https://github.com/ImageMagick/ImageMagick.git && cd ImageMagick && git checkout 7.0.11-4

ENV CFLAGS="--closure 1"
RUN cd jpeg-turbo && emcmake cmake -G"Unix Makefiles" -DENABLE_SHARED=0 -DWITH_SIMD=0 . && emmake make $MAKE_JOBS && emmake make install

#ENV CFLAGS="-I/opt/libjpeg-turbo/include/"
ENV CFLAGS="-I/opt/libjpeg-turbo/include/ -O3 --closure 1"
ENV CPPFLAGS="-I/opt/libjpeg-turbo/include/"
ENV LDFLAGS="-L/opt/libjpeg-turbo/lib32/"
ENV CXXFLAGS="$CFLAGS"
RUN cd ImageMagick && emconfigure ./configure --disable-shared --disable-hdri --disable-docs --without-threads --without-magick-plus-plus && sed -i 's/^\(EXEEXT =\).*/\1 .html/' Makefile && emmake make $MAKE_JOBS

ENTRYPOINT (cd ImageMagick/utilities; python3 -m http.server 80)

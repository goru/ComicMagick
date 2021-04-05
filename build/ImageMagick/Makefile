all: magick.tar.gz

magick.tar.gz: build
	docker run --rm -i image_magick /bin/bash -c "tar -zcf - -C ImageMagick utilities/magick.{html,js,wasm} wasm/magick.{js,wasm}" >magick.tar.gz

.PHONY: build
build:
	docker build -t image_magick --build-arg MAKE_JOBS="-j$(shell grep processor /proc/cpuinfo | wc -l)" .

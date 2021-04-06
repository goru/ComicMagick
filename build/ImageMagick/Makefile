all: build

.PHONY: build
build:
	docker build -t image_magick .
	docker run --rm -i image_magick /bin/bash -c "tar -cf - -C ImageMagick/wasm magick.{js,wasm}" | tar xvf -

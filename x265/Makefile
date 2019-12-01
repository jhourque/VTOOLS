install-dep:
	@sudo apt-get install -y cmake-curses-gui ccmake nasm

clone:
	@rm -rf x265
	@git clone https://github.com/videolan/x265.git

build_multilib:
	@cp -f ./multilib.sh x265/build/linux
	@cd x265/build/linux; MAKEFLAGS=-j4 ./multilib.sh

install:
	@mkdir -p ../.bin
	@cp x265/build/linux/8bit/x265 ../.bin

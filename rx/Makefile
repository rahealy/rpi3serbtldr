#
# Makefile
#  Rust cargo doesn't quite have the functionality we need yet so using
#  a very simple makefile to automate building.
#
# Targets:
#
#  all - Builds debug version.
#  release - Builds optimized release version.
#  clean - Cleans the build directories
#
# Example:
#  $ make clean release
#

TARGET = aarch64-unknown-none
BINARY = rpi3serbtldr_rx

all: debug

debug:
	cargo xrustc --target=$(TARGET)
	cp ./target/$(TARGET)/debug/$(BINARY) .
	cargo objcopy -- --strip-all -O binary ./$(BINARY)

release:
	cargo xrustc --target=$(TARGET) --release
	cp ./target/$(TARGET)/release/$(BINARY) .
	cargo objcopy -- --strip-all -O binary ./$(BINARY)

clean:
	cargo clean
	-rm $(BINARY)

# Command Palette

## v0 Spec

1. Embedded palettes *must* check for presence of higher level (browser extension, native app) palettes and defer behavior to those if the higher level palette supports the spec version

2. Higher level palettes register their presence by setting the `window.__COMMAND_PALETTE` global to an object with two keys:

  1. `name` - the name of the higher order palette
	2. `specVersion` - the supported spec version
# C++

- No `using` at namespace scope in headers.
- Make some effort not to `#include` other projects' headers in your public headers[^public-headers] (avoids forcing build-dependencies upon dependants).

[^public-headers]: Headers are "public" when it is intended that other projects might `#include` them.

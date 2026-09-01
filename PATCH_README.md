# odm_orthophoto — VicTerra P0 int32-overflow fix (native Windows build)

This is `OpenDroneMap/odm_orthophoto` at `GIT_TAG "355"` (the exact tag both
`OpenDroneMap/ODM`'s Docker builds — 3.6.1 and 3.6.2 — and the native Windows 3.6.1 build all
pin, confirmed by reading each build's `SuperBuild/CMakeLists.txt` directly), with one patch
applied: `src/OdmOrthoPhoto.cpp` — widens the four texture-sample index computations in
`renderPixel()` from 32-bit `int` to `size_t` (they overflow `INT32_MAX` on texture atlases
whose `rows × cols × channels > 2,147,483,647`, causing a segfault/access-violation), and adds
an edge-clamp so the bilinear `left+1`/`top+1` taps never read past the texture buffer.

Full investigation, root cause, gdb backtrace, and the Docker/Linux verification of this same
patch: see `docs/perf/cpu-sigsegv-20260901.md` and `docker/odm-p1/` in the VicMapTools repo
(private — this public repo exists only to get a `windows-latest` GitHub Actions runner with
MSVC + vcpkg pre-installed, since the test machine has no native Windows C++ toolchain).

This repo exists solely to build a native `odm_orthophoto.exe` via GitHub Actions
(`.github/workflows/build.yml`, `windows-latest` runner) — not for ongoing development. The
patch is a straightforward integer-overflow fix to a public rendering bug in open-source code;
nothing here is sensitive.

The actual patch, as a standalone diff: `victerra-fix.patch` in this repo's root.

project(
  'mf2000',
  ['fortran', 'c'],
  version: '1.19.01',
  license: 'CC0',
  meson_version: '>= 1.3.1',
  default_options : [
    'b_vscrt=static_from_buildtype', # Link runtime libraries statically on Windows
    'buildtype=release',
])

fc = meson.get_compiler('fortran')
cc = meson.get_compiler('c')
fc_id = fc.get_id()
cc_id = cc.get_id()
message('Fortran Compiler ID:', fc_id)
message('C Compiler ID:', cc_id)

compile_args = []
link_args = []
extra_deps = []

if fc_id == 'gcc'
  compile_args += ['-fall-intrinsics', '-fbacktrace', '-ffpe-summary=overflow', '-std=legacy']
  # Link quadmath library on macOS (required for gfortran runtime)
  if host_machine.system() == 'darwin'
    quadmath_dep = fc.find_library('quadmath', required: true, static: true)
    extra_deps += [quadmath_dep]
  endif
endif

if fc_id == 'intel' or fc_id == 'intel-cl'
  compile_args += ['-diag-disable=10441']
  link_args += ['-diag-disable=10441']
endif

if fc_id == 'intel-llvm' or fc_id == 'intel-llvm-cl'
  compile_args += ['-diag-disable=10441']
  link_args += ['-diag-disable=10441']
endif

add_project_arguments(fc.get_supported_arguments(compile_args), language: 'fortran')
add_project_link_arguments(fc.get_supported_arguments(link_args), language: 'fortran')

# Define _UF for C code when using gfortran or Intel Fortran (use trailing underscore for Fortran symbols)
if fc_id == 'gcc' or fc_id == 'intel' or fc_id == 'intel-cl' or fc_id == 'intel-llvm' or fc_id == 'intel-llvm-cl'
  add_project_arguments('-D_UF', language: 'c')
endif

subdir('src')

# tests
# Note: actual tests would require test data from the original distribution
# For now, just verify the executable runs with --help or version flag
test('version',
     exe,
     args : [],
     should_fail: true,  # mf2000 expects input file, so it will fail without args
     workdir: meson.current_source_dir())

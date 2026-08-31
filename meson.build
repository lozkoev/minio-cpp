project('miniocpp', 'cpp', version : '0.4.0', default_options : ['cpp_std=c++17'])

cxx = meson.get_compiler('cpp')

openssl_dep = dependency('openssl')
httplib_dep = dependency('httplib', method : 'cmake', modules : ['httplib::httplib'])
inih_dep = dependency('INIReader')
nlohmann_json_dep = dependency('nlohmann_json')
pugixml_dep = dependency('pugixml')
zlib_dep = dependency('zlib')

extra_libs = []
if host_machine.system() == 'windows'
  extra_libs = [cxx.find_library('ws2_32'), cxx.find_library('wsock32')]
endif

all_deps = [openssl_dep, httplib_dep, inih_dep, nlohmann_json_dep, pugixml_dep, zlib_dep] + extra_libs
miniocpp_include = include_directories('include')

miniocpp_sources = files(
  'src/args.cc',
  'src/baseclient.cc',
  'src/client.cc',
  'src/credentials.cc',
  'src/error.cc',
  'src/http.cc',
  'src/providers.cc',
  'src/request.cc',
  'src/response.cc',
  'src/select.cc',
  'src/signer.cc',
  'src/sse.cc',
  'src/types.cc',
  'src/utils.cc',
)

# The real library - all .cc files from this repo, not the official minio-cpp
# vcpkg package.
miniocpp_lib = static_library('miniocpp', miniocpp_sources,
  include_directories : miniocpp_include,
  dependencies : all_deps,
)

build_module = (
  (cxx.get_id() == 'msvc' and cxx.version().version_compare('>=19.34'))
  or (cxx.get_id() == 'clang' and cxx.version().version_compare('>=16.0'))
)

if build_module
  if cxx.get_id() == 'msvc'
    module_cpp_args = ['/interface']
  elif cxx.get_id() == 'clang'
    module_cpp_args = ['-x', 'c++-module']
  else
    module_cpp_args = []
  endif
  miniocpp_module_lib = static_library('miniocpp_module', 'modules/miniocpp.cc',
    include_directories : miniocpp_include,
    dependencies : all_deps,
    cpp_args : module_cpp_args,
    override_options : ['cpp_std=c++20'],
  )
  miniocpp_dep = declare_dependency(
    link_with : [miniocpp_lib, miniocpp_module_lib],
    include_directories : miniocpp_include,
    dependencies : all_deps,
  )
else
  miniocpp_dep = declare_dependency(
    link_with : [miniocpp_lib],
    include_directories : miniocpp_include,
    dependencies : all_deps,
  )
endif

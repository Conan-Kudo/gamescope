project(
  'gamescope',
  ['c','cpp'],
  meson_version: '>=0.54.0',
  default_options: ['warning_level=2'],
)

add_project_arguments([
	'-DWLR_USE_UNSTABLE',
], language: ['c','cpp'])

cc = meson.get_compiler('c')
cppc = meson.get_compiler('cpp')

add_project_arguments(cc.get_supported_arguments([
  '-Wno-unused-parameter',
]), language: 'c')

add_project_arguments(cppc.get_supported_arguments([
  '-Wno-unused-parameter',
  '-Wno-missing-field-initializers',
]), language: 'cpp')

dep_x11 = dependency('x11')
dep_xdamage = dependency('xdamage')
dep_xcomposite = dependency('xcomposite')
dep_xrender = dependency('xrender')
dep_xext = dependency('xext')
dep_xxf86vm = dependency('xxf86vm')
dep_xtst = dependency('xtst')

pixman_dep = dependency('pixman-1')
drm_dep = dependency('libdrm')
vulkan_dep = dependency('vulkan')

cc = meson.get_compiler('c')

wayland_server = dependency('wayland-server')
wayland_protos = dependency('wayland-protocols', version: '>=1.17')
pixman         = dependency('pixman-1')
libinput       = dependency('libinput')
xkbcommon      = dependency('xkbcommon')
math           = cc.find_library('m')
thread_dep = dependency('threads')
cap_dep = cc.find_library('cap')
sdl_dep = dependency('SDL2')

wlroots_proj = subproject('wlroots', default_options:
  ['default_library=static', 'examples=false'])
wlroots_static_dep = wlroots_proj.get_variable('wlroots')

shadercompiler = find_program('glslangValidator')

spirv_shader = custom_target('shader_target',
  output : 'composite.h',
  input : 'src/composite.comp.hlsl',
  command : [
    shadercompiler, '-V', '-e', 'main', '--vn',
    'composite_spv', '@INPUT@', '-o', '@OUTPUT@'
  ],
  install : false,
)

liftoff_proj = subproject('libliftoff', default_options:
  ['default_library=static'])
libftoff_dep = liftoff_proj.get_variable('liftoff')

executable(
    'gamescope',
    'src/steamcompmgr.cpp',
    'src/main.cpp',
	'src/wlserver.cpp',
	'src/drm.cpp',
	'src/sdlwindow.cpp',
	'src/vblankmanager.cpp',
	[ 'src/rendervulkan.cpp', spirv_shader ],
    dependencies : [
        dep_x11, dep_xdamage, dep_xcomposite, dep_xrender, dep_xext,
        dep_xxf86vm, pixman_dep, drm_dep, wayland_server, wayland_protos,
        libinput, xkbcommon, math, thread_dep, sdl_dep, wlroots_static_dep,
        vulkan_dep, libftoff_dep, dep_xtst, cap_dep
    ],
    install: true,
)

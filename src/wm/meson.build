custom_target('desktop-file-wm',
    input : 'budgie-wm.desktop.in',
    output : 'budgie-wm.desktop',
    command : [intltool, '--desktop-style', podir, '@INPUT@', '@OUTPUT@'],
    install : true,
    install_dir : join_paths(datadir, 'applications'))


budgie_wm_sources = [
    'background.vala',
    'ibus.vala',
    'keyboard.vala',
    'main.vala',
    'shim.vala',
    'wm.vala',
]

vapi_mutter = 'libmutter'
dep_mutter = dependency('libmutter', version: gnome_minimum_version, required: false)

if not dep_mutter.found()
    dep_mutter = dependency('libmutter-0', version: gnome_minimum_version, required: false)
    if dep_mutter.found()
        message('Using stable libmutter-0 ABI from GNOME 3.24')
        vapi_mutter = 'libmutter-0'
    endif
endif

# now try libmutter-1 ...
if not dep_mutter.found()
    dep_mutter = dependency('libmutter-1', version: gnome_minimum_version, required: false)
    if dep_mutter.found()
        message('Using new libmutter-1 ABI from GNOME 3.26')
        vapi_mutter = 'libmutter-1'
    endif
endif

# now try libmutter-2 ...
if not dep_mutter.found()
    dep_mutter = dependency('libmutter-2', version: gnome_minimum_version)
    if dep_mutter.found()
        message('Using new libmutter-2 ABI from GNOME 3.28')
        vapi_mutter = 'libmutter-2'
    endif
endif

budgie_wm_deps = [
    link_libconfig,
    dep_mutter,
    dep_gnomedesktop,
    dep_gsettings,
    dep_ibus,
]

budgie_wm_vala_args =  [
        '--pkg', 'gio-unix-2.0',
        '--pkg', 'ibus-1.0',
        '--pkg', 'gnome-desktop-3.0',
        '--pkg', vapi_mutter,
        '--pkg', 'budgie-config',
        '--pkg', 'gsettings-desktop-schemas',
        '--vapidir', join_paths(meson.source_root(), 'vapi'),
        '--vapidir', dir_libconfig,
]

rpath = ''

if dep_mutter.version().version_compare('>=3.21.0')
    rpath = dep_mutter.get_pkgconfig_variable('typelibdir')
    budgie_wm_vala_args += ['--girdir', rpath]
endif

executable(
    'budgie-wm', budgie_wm_sources,
    dependencies: budgie_wm_deps,
    include_directories: extra_includes,
    install: true,
    vala_args: budgie_wm_vala_args,
    c_args: [
        '-DGNOME_DESKTOP_USE_UNSTABLE_API',
    ],
    install_rpath: rpath,
)

# gschemas
install_data(
    'com.solus-project.budgie.wm.gschema.xml',
    install_dir: join_paths(datadir, 'glib-2.0', 'schemas'),
)

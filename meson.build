project('hibernated', 'd',
    meson_version: '>=0.40',
    license: 'BSL-1.0',
    version: '0.3.2'
)

project_soversion = '0'

#
# Dependencies
#
pkgc = import('pkgconfig')
ddbc_pg_dep = dependency('ddbc-pgsql', version: '>= 0.3.7', fallback: ['ddbc', 'ddbc_pg_dep'])

#
# Sources
#
hibernated_src = [
    'source/hibernated/annotations.d',
    'source/hibernated/core.d',
    'source/hibernated/dialect.d',
    'source/hibernated/dialects/mysqldialect.d',
    'source/hibernated/dialects/pgsqldialect.d',
    'source/hibernated/dialects/sqlitedialect.d',
    'source/hibernated/metadata.d',
    'source/hibernated/query.d',
    'source/hibernated/session.d',
    'source/hibernated/tests.d',
    'source/hibernated/type.d'
]

src_dir = include_directories('source/')

# determine the right version flag for the compiler we use
dc = meson.get_compiler('d')
dc_version_prefix = '-fversion='
if dc.get_id() == 'llvm'
    dc_version_prefix = '-d-version='
elif dc.get_id() == 'dmd'
    dc_version_prefix = '-version='
endif

#
# Targets
#

pg_module_versions = ['USE_PGSQL', 'Derelict_Static']
hibernated_pg_lib = library('hibernated-pgsql',
        [hibernated_src],
        include_directories: [src_dir],
        dependencies: [ddbc_pg_dep],
        install: true,
        version: meson.project_version(),
        soversion: project_soversion,
        d_module_versions: pg_module_versions
)
pkgc.generate(name: 'hibernated-pgsql',
              libraries: hibernated_pg_lib,
              subdirs: 'd/hibernated',
              version: meson.project_version(),
              requires: ['ddbc-pgsql'],
              description: 'ORM for D language (PostgreSQL library).',
              d_module_versions: pg_module_versions
)

# prepare flags for declare_dependency
pg_dep_compile_flags = []
foreach m : pg_module_versions
    pg_dep_compile_flags += dc_version_prefix + m
endforeach

# to allow others to easily use this as a subproject
hibernated_pg_dep = declare_dependency(
    link_with: [hibernated_pg_lib],
    include_directories: [src_dir],
    dependencies: [ddbc_pg_dep],
    compile_args: pg_dep_compile_flags
)

#
# Install
#
install_subdir('source/hibernated/', install_dir: 'include/d/hibernated/')

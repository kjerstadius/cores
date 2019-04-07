project('teensy_core', ['c', 'cpp'],
        default_options : ['b_staticpic=false',
                           'b_lto=true',
                           'buildtype=minsize',
                           'c_std=c11',
                           'cpp_std=c++14',
                           'default_library=static'],
        meson_version : '>=0.49.0',
        version : '0.99')

assert(meson.is_cross_build(), 'Build not initialized as cross build. '
                               + 'Native build is unfortunately not possible.')

# Handle options:
teensy_variant_info = {'3.0': {'mcu': 'MK20DX128'},
                       '3.1': {'mcu': 'MK20DX256'},
                       '3.2': {'mcu': 'MK20DX256'},
                       '3.5': {'mcu': 'MK64FX512'},
                       '3.6': {'mcu': 'MK66FX1M0'},
                       'LC':  {'mcu': 'MKL26Z64'}}
variant = get_option('variant')
assert(teensy_variant_info.has_key(variant),
       'Teensy variant ' + variant + ' not recognized.')
mcu = teensy_variant_info.get(variant).get('mcu')
opts = []
opts += '-D__' + mcu.to_upper() + '__'
opts += '-DARDUINO=' + '@0@'.format(get_option('arduino_version'))
opts += '-DF_CPU=' + get_option('cpu_frequency') + '000000'
opts += '-DLAYOUT_' + get_option('keyboard_layout').to_upper()
opts += '-DTEENSYDUINO=' + '@0@'.format(get_option('teensyduino_version'))
opts += '-DUSB_' + get_option('usb_mode').to_upper()

subdir('teensy3')

loader_cli = find_program('teensy_loader_cli',
                          get_option('teensy_loader_path')
                          + '/teensy_loader_cli',
                          disabler : true,
                          native : true,
                          required : false)
if not loader_cli.found()
  warning('Teensy loader CLI executable not found. Flashing will be disabled.')
endif
run_target('flash', command : [loader_cli, '--mcu=' + mcu.to_lower(),
                               '-s', '-v', '-w', blinky_hex])

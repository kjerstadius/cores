project('teensy_core', ['c', 'cpp'],
        default_options : ['b_staticpic=false',
                           'buildtype=minsize',
                           'c_std=c11',
                           'cpp_std=c++14',
                           'default_library=static'],
        version : '0.99')

if not meson.is_cross_build()
  error('Build not initialized as cross build. '
        + 'Native build is unfortunately not possible.')
endif

# Handle options
opts = []
mcu = get_option('mcu')
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

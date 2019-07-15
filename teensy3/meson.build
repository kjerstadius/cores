c_srcs = ['analog.c',
          'eeprom.c',
          'keylayouts.c',
          'math_helper.c',
          'mk20dx128.c',
          'nonstd.c',
          'pins_teensy.c',
          'ser_print.c',
          'serial1.c',
          'serial2.c',
          'serial3.c',
          'serial4.c',
          'serial5.c',
          'serial6.c',
          'serial6_lpuart.c',
          'touch.c',
          'usb_desc.c',
          'usb_dev.c',
          'usb_joystick.c',
          'usb_keyboard.c',
          'usb_mem.c',
          'usb_midi.c',
          'usb_mouse.c',
          'usb_mtp.c',
          'usb_rawhid.c',
          'usb_seremu.c',
          'usb_serial.c',
          'usb_touch.c']

cpp_srcs = ['AudioStream.cpp',
            'DMAChannel.cpp',
            'EventResponder.cpp',
            'HardwareSerial1.cpp',
            'HardwareSerial2.cpp',
            'HardwareSerial3.cpp',
            'HardwareSerial4.cpp',
            'HardwareSerial5.cpp',
            'HardwareSerial6.cpp',
            'IPAddress.cpp',
            'IntervalTimer.cpp',
            'Print.cpp',
            'Stream.cpp',
            'Tone.cpp',
            'WMath.cpp',
            'WString.cpp',
            'avr_emulation.cpp',
            'new.cpp',
            'usb_audio.cpp',
            'usb_flightsim.cpp',
            'usb_inst.cpp',
            'yield.cpp']

# Note: The optimised memset.S is only used when optimising for size (-Os).
# The assembly optimizations also don't work for the Teensy LC as they're
# intended for the M4. See https://github.com/PaulStoffregen/cores/issues/272
if variant == 'LC'
  s_srcs = []
else
  s_srcs = ['memcpy-armv7m.S',
            'memset.S']
endif

includes = include_directories(['.', 'avr', 'util'])

libteensycore = library('teensycore-teensy' + variant_nodot,
                        [c_srcs, cpp_srcs, s_srcs],
                        c_args : opts,
                        cpp_args : opts,
                        include_directories : includes,
                        build_by_default : true,
                        install : do_install)
libteensycore_dep = declare_dependency(include_directories: includes,
                                       link_whole: libteensycore)

libteensycore_main = library('teensycore-main-teensy' + variant_nodot,
                             'main.cpp',
                             c_args : opts,
                             cpp_args : opts,
                             include_directories : includes,
                             build_by_default : true,
                             install : do_install)
libteensycore_main_dep = declare_dependency(include_directories : includes,
                                            link_whole : libteensycore_main)

link_script = files(mcu.to_lower() + '.ld')
link_script_flag = '-T@0@/@1@'.format(meson.source_root(), link_script[0])
blinky_elf = executable('blinky-teensy' + variant_nodot,
                        'main.cpp',
                        c_args : opts,
                        cpp_args : [opts, '-DUSING_MAKEFILE'],
                        dependencies : libteensycore_dep,
                        link_args : link_script_flag,
                        link_depends : link_script,
                        name_suffix : 'elf',
                        build_by_default : true,
                        install : do_install)

size_exe = find_program('size')
run_target('blinky.size', command : [size_exe, blinky_elf])

objcopy = find_program('objcopy')
objcopy_flags = ['-O', 'ihex', '-R', '.eeprom']
blinky_hex = custom_target('blinky-hex',
                           command : [objcopy, objcopy_flags,
                                      '@INPUT@', '@OUTPUT@'],
                           input : blinky_elf,
                           output : 'blinky-teensy' + variant_nodot + '.hex',
                           build_by_default : true,
                           install : do_install,
                           install_dir : get_option('bindir'))

loader_cli = find_program('teensy_loader_cli',
                          get_option('teensy_loader_path')
                          + '/teensy_loader_cli',
                          disabler : true,
                          native : true,
                          required : false)
if not loader_cli.found()
  warning('Teensy loader CLI executable not found. Flashing will be disabled.')
endif
run_target('blinky.flash', command : [loader_cli, '--mcu=' + mcu.to_lower(),
                                      '-s', '-v', '-w', blinky_hex])

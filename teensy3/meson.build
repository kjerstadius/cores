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

teensy3_core_lib = library('core_teensy3', [c_srcs, cpp_srcs],
                           build_by_default : true,
                           c_args : opts,
                           cpp_args : opts)

link_script = mcu.to_lower() + '.ld'
map_flag = '-T@0@/@1@'.format(meson.current_source_dir(), link_script)
blinky_elf = executable('blinky', ['main.cpp'],
                        build_by_default : true,
                        c_args : opts,
                        cpp_args : [opts, '-DUSING_MAKEFILE'],
                        link_args : map_flag,
                        link_depends : link_script,
                        link_whole : teensy3_core_lib,
                        name_suffix : 'elf')

size_exe = find_program('size')
run_target('size', command : [size_exe, blinky_elf])

objcopy = find_program('objcopy')
objcopy_flags = ['-O', 'ihex', '-R', '.eeprom']
blinky_hex = custom_target('blinky-hex',
                           build_by_default : true,
                           command : [objcopy, objcopy_flags,
                                      '@INPUT@', '@OUTPUT@'],
                           input : blinky_elf,
                           output : 'blinky.hex')

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
                           c_args : [opts, extra_args],
                           cpp_args : [opts, extra_args])

t3_core_srcs = files(c_srcs, cpp_srcs)

cc = meson.get_compiler('c')
libm = cc.find_library('libm')
link_script = mcu.to_lower() + '.ld'
map_flag = '-T@0@/@1@'.format(meson.current_source_dir(), link_script)
main_elf = executable('main', ['main.cpp', t3_core_srcs],
                      build_by_default : true,
                      c_args : [opts, extra_args],
                      cpp_args : [opts, extra_args],
                      dependencies : [libm],
                      link_args : [map_flag],
                      link_depends : [link_script],
                      # Why does linking with the static library not work?
                      # link_with : [teensy3_core_lib],
                      name_suffix : 'elf')

size_exe = find_program('size')
run_target('size', command : [size_exe, main_elf])

objcopy = find_program('objcopy')
objcopy_flags = ['-O', 'ihex', '-R', '.eeprom']
main_hex = custom_target('main_hex',
                         build_by_default : true,
                         command : [objcopy, objcopy_flags,
                                    '@INPUT@', '@OUTPUT@'],
                         input : main_elf,
                         output : 'main.hex')

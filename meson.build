project('libteensycore', ['c', 'cpp'],
        version : '1.45',
        default_options : ['b_lto=true',
                           'b_staticpic=false',
                           'buildtype=minsize',
                           'c_std=c11',
                           'cpp_std=gnu++14',
                           'default_library=static'],
        meson_version : '>=0.49.0')

assert(meson.is_cross_build(), 'Build not initialized as cross build. '
                               + 'Native build is unfortunately not possible.')

# Handle options:
teensy_variant_info = {'3.0': {'mcu': 'MK20DX128', 'f_cpu_max': 96},
                       '3.2': {'mcu': 'MK20DX256', 'f_cpu_max': 120},
                       '3.5': {'mcu': 'MK64FX512', 'f_cpu_max': 168},
                       '3.6': {'mcu': 'MK66FX1M0', 'f_cpu_max': 240},
                       'LC':  {'mcu': 'MKL26Z64', 'f_cpu_max': 48}}
variant = get_option('variant')
assert(teensy_variant_info.has_key(variant),
       'Teensy variant ' + variant + ' not recognized.')
variant_nodot = ''.join(variant.split('.'))
variant_info = teensy_variant_info.get(variant)
mcu = variant_info.get('mcu')
f_cpu_max = variant_info.get('f_cpu_max')
assert(get_option('cpu_frequency').to_int() <= f_cpu_max,
       'CPU frequency exceeds limit for Teensy ' + variant + '. The maximum '
       + '(overclocked) CPU frequency for Teensy ' + variant
       + ' is @0@, but the selected CPU frequency is '.format(f_cpu_max)
       + get_option('cpu_frequency') + '.')
opts = []
opts += '-D__' + mcu.to_upper() + '__'
opts += '-DARDUINO=' + '@0@'.format(get_option('arduino_version'))
opts += '-DF_CPU=' + get_option('cpu_frequency') + '000000'
opts += '-DLAYOUT_' + get_option('keyboard_layout').to_upper()
opts += '-DTEENSYDUINO=' + '@0@'.format(get_option('teensyduino_version'))
opts += '-DUSB_' + get_option('usb_mode').to_upper()

if variant == 'LC'
  subdir('teensy3')
else
  subdir('teensy' + variant.split('.')[0])
endif

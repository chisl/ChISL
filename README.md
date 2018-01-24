# ChISL
Chip Interface Sepcification Language

## Chisl Syntax ##

Chisl has a quite simple syntax for defining interfaces to integrated circuits (sensors, transceivers, PMICs, etc.). To get a first impression browse to [http://chisl.io](http://chisl.io) and open the file `MPU60X0` (file->open->MPU-60X0). What you see is the interface definition for the MPU-60X0 (a chip for 6-axis motion tracking). The interface definition contains general 
information about the chip and detailed information about the function of each
register of the chip.

 <a name="info"></a>
### Info block ###

The first block is the `Info` block that contains basic information like name, version, url, etc. Blocks are defined by a header, `Info:` in this case and an
indented body.

```python
Info:
    name = MPU-60X0
    version = 0.1
    url = https://example.com/DataSheet.pdf
    date = 20160901
    comment = MPU6000,MPU6050
```

<a name="register"></a>
### Register block ###

The blocks that follow the `Info` block define registers, for example

```python
REG 'h1A = CONFIG:
    § This register configures the external Frame Synchronization (FSYNC) pin
      sampling and ... §
    MODE rw
    DEFAULT 'h00
    BIT 7..6 = unused
    BIT 5..3 = EXT_SYNC_SET:
        § An external signal connected to the FSYNC pin can be sampled 
          by configuring EXT_SYNC_SET ... §
        '0 = Disabled
        '1 = TEMP_OUT_L
        '2 = GYRO_XOUT_L
        '3 = GYRO_YOUT_L
        '4 = GYRO_ZOUT_L
        '5 = ACCEL_XOUT_L
        '6 = ACCEL_YOUT_L
        '7 = ACCEL_ZOUT_L
    BIT 2..0 = DLPF_CFG     § this is an inline comment 
```
The header of a register block has the format `REG <register-address> = <name>`.
The format of the register address number is somwhat unusual and explained [below](#numbers).

The body of a register block has different entries (in no specific order).

 <a name="mode"></a>
#### `MODE` ####
`MODE` is followed by a string that identifies read, write, etc. modes.
There can be only one such line in the register body.

 <a name="default"></a>
#### `DEFAULT` ####
`DEFAULT` is followed by a number in Chisl format (see [below](#numbers)).
This is the default value of the register. 
There can be only one such line in the register body.

<a name="bit"></a>
#### `BIT` ####
The bit definition can be a single line with format 
`BIT <m>..<n>  = <name>` where `<m>..<n>` defines a bit range. For example 
`3..0` is the bit range 3,2,1,0.

```
BIT 7..6 = unused
```

The bit definition can also be a block, in which case the header 
`BIT <m>..<n>  = <name>:` is followed by a body containing comment(s) and/or
assignmens of the form `<number> = <name>`.

```
    BIT 5..3 = EXT_SYNC_SET:
        § An external signal connected to the FSYNC pin can be sampled 
          by configuring EXT_SYNC_SET ... §
        '0 = Disabled
        '1 = TEMP_OUT_L
        § and so on §
```

#### `§ ... §` Block comment ####
The `§...§` construct is a block comment (see section [Comments](#comments) below).

<a name="comments"></a>
### Comments ###
There are two types of comments:

- **Inline comments** begin with a `§` and continue to the end of the line.
- **Block comments** are enclosed in `§`'s and can contain several lines.

<a name="names"></a>
### Names ###
[Registers](#register) and [bit ranges](#bit) can have names. Names follow an equal `=` sign and have to conform to C variable naming rules. 
Not that names must **not** be enclosed in quotes.


<a name="numbers"></a>
### Chisl number format ###
Register addresses and values are numbers written in a special format:

```Verilog
8'hff       § Hex number FF
6'b1010     § binary number 001010
9'o777      § octal number
16'd1000    § decimal 1000, 16 bits long
```
The general format is `[bitlength]'[b|o|d|h]<number>`. The bitlength is optional (default 8) and the radix is optional, too (default `d` for decimal).

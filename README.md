# Unix dump format

<https://en.wikipedia.org/wiki/Dump_(program)>

## Magic bytes

Unix file(1) has magic patterns for 7 variations of the format (author: Christos Zoulas):

<https://github.com/file/file/blob/master/magic/Magdir/dump>

Ignoring everything related to extracted properties, the signatures are:

    24	belong	60012		new-fs dump file (big endian),

    24	belong	60011		old-fs dump file (big endian),

    24	lelong	60012		new-fs dump file (little endian),
    # to correctly recognize '*.mo' GNU message catalog (little endian)
    !:strength - 15

    24	lelong	60011		old-fs dump file (little endian),

    24	belong	0x19540119	new-fs dump file (ufs2, big endian),

    24	lelong	0x19540119	new-fs dump file (ufs2, little endian),

    18	leshort	60011		old-fs dump file (16-bit, assuming PDP-11 endianness)

Translating to hex strings:

|File|Hexadecimal string|
|:--|:--|
|24&emsp;belong&emsp;60012&emsp;new-fs dump file (big endian)|00 00 EA 6C|
|24&emsp;belong&emsp;60011&emsp;old-fs dump file (big endian)|00 00 EA 6B|
|24&emsp;lelong&emsp;60012&emsp;new-fs dump file (little endian)|6C EA 00 00|
|24&emsp;lelong&emsp;60011&emsp;old-fs dump file (little endian)|6B EA 00 00|
|24&emsp;belong&emsp;0x19540119&emsp;new-fs dump file (ufs2, big endian)|19 54 01 19|
|24&emsp;lelong&emsp;0x19540119&emsp;new-fs dump file (ufs2, little endian)|19 01 54 19|
|18&emsp;leshort&emsp;60011&emsp;old-fs dump file (16-bit, assuming PDP-11 endianness)|6B EA|

## Tika mimetype definitions

Below is a set of mimetype definitions for Apache Tika, derived from the file(1) entries (submitted to Tika, [pull request pending](https://github.com/apache/tika/pull/266)):

```xml
<mime-type type="application/x-tika-unix-dump">
  <_comment>Unix dump file</_comment>
  <tika:link>https://en.wikipedia.org/wiki/Dump_(program)</tika:link>
</mime-type>

<!-- Unix dump magic adapted from file(1) magic by Christos Zoulas
Note that within the 'old' and 'new' dump formats below, further
subdivisions are possible, but not sure how to best report them (if at
all) -->

<mime-type type="application/x-tika-unix-dump-old">
  <_comment>Unix dump file, old file system</_comment>
  <sub-class-of type="application/x-tika-unix-dump" />
  <magic priority="50">
    <!-- big endian -->
    <match value="0x0000EA6B" type="string" offset="24"/>
    <!-- little endian -->
    <match value="0x6BEA0000" type="string" offset="24"/>
    <!-- 16-bit, assuming PDP-11 endianness -->
  <match value="0x6BEA" type="string" offset="18"/>
  </magic>
</mime-type>

<mime-type type="application/x-tika-unix-dump-new">
  <_comment>Unix dump file, new file system</_comment>
  <sub-class-of type="application/x-tika-unix-dump" />
  <magic priority="50">
    <!-- big endian -->
    <match value="0x0000EA6C" type="string" offset="24"/>
    <!-- little endian. Note that file(1) gives this lower priority
    because of possible conflict with GNU message catalog, but this
    format is currently not covered by Tika -->
    <match value="0x6CEA0000" type="string" offset="24"/>
    <!-- ufs2, big endian -->
    <match value="0x19540119" type="string" offset="24"/>
    <!-- ufs2, little endian -->
    <match value="0x19015419" type="string" offset="24"/>
  </magic>
</mime-type>
```
## Test files

Directory [files](./files/) contains minimal sample files for each of the aforementioned varieties, hand-crafted according to the *file* magic patterns. Note that these files *only* contain the magic patterns and some leading null-bytes, they are not full (let alone valid) dump files!

## Resources

- [dump (program)](https://en.wikipedia.org/wiki/Dump_(program))
- [Dump/restore utilities](http://dump.sourceforge.net/)
- [Is dump really deprecated?](http://dump.sourceforge.net/isdumpdeprecated.html)
- [Using Dump for Linux/Unix data backup](https://searchdatabackup.techtarget.com/tip/Using-Dump-for-Linux-Unix-data-backup) - describes major advantages and disadvantages of using dump to back up your data

<!--
Quote:

> There are a lot of switches, not much explanation and dump assumes you mean what you tell it. It's powerful, but when used carelessly it can hopelessly corrupt your backups, or your entire file system.
-->

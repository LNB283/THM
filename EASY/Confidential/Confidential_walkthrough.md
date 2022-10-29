# Insekube Walkthrough
### Link
- https://tryhackme.com/room/confidential
---

First, start the machine and then you can access the target machine from the split view.

Now, we have access to the pdf : **Repdf.pdf**. 

When we open the pdf and we have a QR code with a red flag that hides it. Our objective is to delete this red flag and have access to the QR code.

I like to use **pdfimages** for this kind of challenge.

```
pdfimages version 0.86.1
Copyright 2005-2020 The Poppler Developers - http://poppler.freedesktop.org
Copyright 1996-2011 Glyph & Cog, LLC
Usage: pdfimages [options] <PDF-file> <image-root>
  -f <int>       : first page to convert
  -l <int>       : last page to convert
  -png           : change the default output format to PNG
  -tiff          : change the default output format to TIFF
  -j             : write JPEG images as JPEG files
  -jp2           : write JPEG2000 images as JP2 files
  -jbig2         : write JBIG2 images as JBIG2 files
  -ccitt         : write CCITT images as CCITT files
  -all           : equivalent to -png -tiff -j -jp2 -jbig2 -ccitt
  -list          : print list of images instead of saving
  -opw <string>  : owner password (for encrypted files)
  -upw <string>  : user password (for encrypted files)
  -p             : include page numbers in output file names
  -q             : don't print any messages or errors
  -v             : print copyright and version info
  -h             : print usage information
  -help          : print usage information
  --help         : print usage information
  -?             : print usage information
```

```
pdfimages -png Repdf.pdf Repdf_result
```

I finally obtain 3 files:

- the file with the QR Code without the red flag
- the red flag picture
- the white flag picture

Now I open the file with the QR code and scan it

**Question**
#### Uncover and scan the QR code to retrieve the flag!
**Answer** 
#### flag{e08e6ce2f077a1b420cfd4a5d1a57a8d}
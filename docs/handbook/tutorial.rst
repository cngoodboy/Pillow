快速入门
========

.. note:: 译者注：专有名词尽量不翻译，比如bands, modes等请参考概念文档。

使用 Image class
---------------------

PIL最重要的class是
:py:class:`~PIL.Image.Image` class, 你可以通过多种方法创建这个类的实例; 你可以从文件加载图像, 或者处理其他图像, 或者从 scratch 创建.

要从文件加载图像, 使用 :py:func:`~PIL.Image.open` 函数，
在 :py:mod:`~PIL.Image` 模块::

    >>> from PIL import Image
    >>> im = Image.open("lena.ppm")

加载成功将返回一个 :py:class:`~PIL.Image.Image` 对象。
你现在可以使用示例属性检查文件内容::

    >>> from __future__ import print_function
    >>> print(im.format, im.size, im.mode)
    PPM (512, 512) RGB

:py:attr:`~PIL.Image.Image.format` 这个属性标识了图像来源。如果图像不是从文件读取它的值就是None。size属性是一个二元tuple，包含width和height（宽度和高度，单位都是px）。
:py:attr:`~PIL.Image.Image.mode` 属性定义了图像bands的数量和名称，以及像素类型和深度。常见的modes 有 “L”
(luminance) 表示灰度图像, “RGB” 表示真彩色图像, and “CMYK” 表示出版图像。

如果文件打开错误，返回 :py:exc:`IOError` 错误。

Once you have an instance of the :py:class:`~PIL.Image.Image` class, you can use
the methods defined by this class to process and manipulate the image. For
example, let’s display the image we just loaded::

    >>> im.show()

.. note::

    The standard version of :py:meth:`~PIL.Image.Image.show` is not very
    efficient, since it saves the image to a temporary file and calls the
    :command:`xv` utility to display the image. If you don’t have :command:`xv`
    installed, it won’t even work. When it does work though, it is very handy
    for debugging and tests.

The following sections provide an overview of the different functions provided in this library.

读写图像
--------------------------

The Python Imaging Library supports a wide variety of image file formats. To
read files from disk, use the :py:func:`~PIL.Image.open` function in the
:py:mod:`~PIL.Image` module. You don’t have to know the file format to open a
file. The library automatically determines the format based on the contents of
the file.

To save a file, use the :py:meth:`~PIL.Image.Image.save` method of the
:py:class:`~PIL.Image.Image` class. When saving files, the name becomes
important. Unless you specify the format, the library uses the filename
extension to discover which file storage format to use.

转换文件格式到JPEG
^^^^^^^^^^^^^^^^^^^^^

::

    from __future__ import print_function
    import os, sys
    from PIL import Image

    for infile in sys.argv[1:]:
        f, e = os.path.splitext(infile)
        outfile = f + ".jpg"
        if infile != outfile:
            try:
                Image.open(infile).save(outfile)
            except IOError:
                print("cannot convert", infile)

A second argument can be supplied to the :py:meth:`~PIL.Image.Image.save`
method which explicitly specifies a file format. If you use a non-standard
extension, you must always specify the format this way:

创建 JPEG 缩略图
^^^^^^^^^^^^^^^^^^^^^^

::

    from __future__ import print_function
    import os, sys
    from PIL import Image

    size = (128, 128)

    for infile in sys.argv[1:]:
        outfile = os.path.splitext(infile)[0] + ".thumbnail"
        if infile != outfile:
            try:
                im = Image.open(infile)
                im.thumbnail(size)
                im.save(outfile, "JPEG")
            except IOError:
                print("cannot create thumbnail for", infile)

It is important to note that the library doesn’t decode or load the raster data
unless it really has to. When you open a file, the file header is read to
determine the file format and extract things like mode, size, and other
properties required to decode the file, but the rest of the file is not
processed until later.

This means that opening an image file is a fast operation, which is independent
of the file size and compression type. Here’s a simple script to quickly
identify a set of image files:

验证图像文件
^^^^^^^^^^^^^^^^^^^^

::

    from __future__ import print_function
    import sys
    from PIL import Image

    for infile in sys.argv[1:]:
        try:
            with Image.open(infile) as im:
                print(infile, im.format, "%dx%d" % im.size, im.mode)
        except IOError:
            pass

剪切，粘贴，合并图像
------------------------------------

The :py:class:`~PIL.Image.Image` class contains methods allowing you to
manipulate regions within an image. To extract a sub-rectangle from an image,
use the :py:meth:`~PIL.Image.Image.crop` method.

从图像中复制出一个矩形
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    box = (100, 100, 400, 400)
    region = im.crop(box)

The region is defined by a 4-tuple, where coordinates are (left, upper, right,
lower). The Python Imaging Library uses a coordinate system with (0, 0) in the
upper left corner. Also note that coordinates refer to positions between the
pixels, so the region in the above example is exactly 300x300 pixels.

The region could now be processed in a certain manner and pasted back.

处理复制的矩形并放回到原图
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    region = region.transpose(Image.ROTATE_180)
    im.paste(region, box)

When pasting regions back, the size of the region must match the given region
exactly. In addition, the region cannot extend outside the image. However, the
modes of the original image and the region do not need to match. If they don’t,
the region is automatically converted before being pasted (see the section on
:ref:`color-transforms` below for details).

Here’s an additional example:

Rolling an image
^^^^^^^^^^^^^^^^

::

    def roll(image, delta):
        "Roll an image sideways"

        xsize, ysize = image.size

        delta = delta % xsize
        if delta == 0: return image

        part1 = image.crop((0, 0, delta, ysize))
        part2 = image.crop((delta, 0, xsize, ysize))
        image.paste(part2, (0, 0, xsize-delta, ysize))
        image.paste(part1, (xsize-delta, 0, xsize, ysize))

        return image

For more advanced tricks, the paste method can also take a transparency mask as
an optional argument. In this mask, the value 255 indicates that the pasted
image is opaque in that position (that is, the pasted image should be used as
is). The value 0 means that the pasted image is completely transparent. Values
in-between indicate different levels of transparency.

The Python Imaging Library also allows you to work with the individual bands of
an multi-band image, such as an RGB image. The split method creates a set of
new images, each containing one band from the original multi-band image. The
merge function takes a mode and a tuple of images, and combines them into a new
image. The following sample swaps the three bands of an RGB image:

分离和合并通道
^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    r, g, b = im.split()
    im = Image.merge("RGB", (b, g, r))

Note that for a single-band image, :py:meth:`~PIL.Image.Image.split` returns
the image itself. To work with individual color bands, you may want to convert
the image to “RGB” first.

几何变换
----------------------

The :py:class:`PIL.Image.Image` class contains methods to
:py:meth:`~PIL.Image.Image.resize` and :py:meth:`~PIL.Image.Image.rotate` an
image. The former takes a tuple giving the new size, the latter the angle in
degrees counter-clockwise.

简单的几何变换
^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    out = im.resize((128, 128))
    out = im.rotate(45) # degrees counter-clockwise

To rotate the image in 90 degree steps, you can either use the
:py:meth:`~PIL.Image.Image.rotate` method or the
:py:meth:`~PIL.Image.Image.transpose` method. The latter can also be used to
flip an image around its horizontal or vertical axis.

旋转图像
^^^^^^^^^^^^^^^^^^^^

::

    out = im.transpose(Image.FLIP_LEFT_RIGHT)
    out = im.transpose(Image.FLIP_TOP_BOTTOM)
    out = im.transpose(Image.ROTATE_90)
    out = im.transpose(Image.ROTATE_180)
    out = im.transpose(Image.ROTATE_270)

There’s no difference in performance or result between ``transpose(ROTATE)``
and corresponding :py:meth:`~PIL.Image.Image.rotate` operations.

A more general form of image transformations can be carried out via the
:py:meth:`~PIL.Image.Image.transform` method.

.. _color-transforms:

颜色变换
----------------

The Python Imaging Library allows you to convert images between different pixel
representations using the :py:meth:`~PIL.Image.Image.convert` method.

颜色模式转换
^^^^^^^^^^^^^^^^^^^^^^^^

::

    im = Image.open("lena.ppm").convert("L")

The library supports transformations between each supported mode and the “L”
and “RGB” modes. To convert between other modes, you may have to use an
intermediate image (typically an “RGB” image).

颜色增强
-----------------

The Python Imaging Library provides a number of methods and modules that can be
used to enhance images.

过滤器
^^^^^^^

The :py:mod:`~PIL.ImageFilter` module contains a number of pre-defined
enhancement filters that can be used with the
:py:meth:`~PIL.Image.Image.filter` method.

应用过滤器
~~~~~~~~~~~~~~~~

::

    from PIL import ImageFilter
    out = im.filter(ImageFilter.DETAIL)

点操作
^^^^^^^^^^^^^^^^

The :py:meth:`~PIL.Image.Image.point` method can be used to translate the pixel
values of an image (e.g. image contrast manipulation). In most cases, a
function object expecting one argument can be passed to the this method. Each
pixel is processed according to that function:

应用点操作
~~~~~~~~~~~~~~~~~~~~~~~~~

::

    # multiply each pixel by 1.2
    out = im.point(lambda i: i * 1.2)

Using the above technique, you can quickly apply any simple expression to an
image. You can also combine the :py:meth:`~PIL.Image.Image.point` and
:py:meth:`~PIL.Image.Image.paste` methods to selectively modify an image:

处理个别bands
~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

    # split the image into individual bands
    source = im.split()

    R, G, B = 0, 1, 2

    # select regions where red is less than 100
    mask = source[R].point(lambda i: i < 100 and 255)

    # process the green band
    out = source[G].point(lambda i: i * 0.7)

    # paste the processed band back, but only where red was < 100
    source[G].paste(out, None, mask)

    # build a new multiband image
    im = Image.merge(im.mode, source)

Note the syntax used to create the mask::

    imout = im.point(lambda i: expression and 255)

Python only evaluates the portion of a logical expression as is necessary to
determine the outcome, and returns the last value examined as the result of the
expression. So if the expression above is false (0), Python does not look at
the second operand, and thus returns 0. Otherwise, it returns 255.

增强
^^^^^^^^^^^

For more advanced image enhancement, you can use the classes in the
:py:mod:`~PIL.ImageEnhance` module. Once created from an image, an enhancement
object can be used to quickly try out different settings.

You can adjust contrast, brightness, color balance and sharpness in this way.

增强图形
~~~~~~~~~~~~~~~~

::

    from PIL import ImageEnhance

    enh = ImageEnhance.Contrast(im)
    enh.enhance(1.3).show("30% more contrast")

Image sequences
---------------

The Python Imaging Library contains some basic support for image sequences
(also called animation formats). Supported sequence formats include FLI/FLC,
GIF, and a few experimental formats. TIFF files can also contain more than one
frame.

When you open a sequence file, PIL automatically loads the first frame in the
sequence. You can use the seek and tell methods to move between different
frames:

Reading sequences
^^^^^^^^^^^^^^^^^

::

    from PIL import Image

    im = Image.open("animation.gif")
    im.seek(1) # skip to the second frame

    try:
        while 1:
            im.seek(im.tell()+1)
            # do something to im
    except EOFError:
        pass # end of sequence

As seen in this example, you’ll get an :py:exc:`EOFError` exception when the
sequence ends.

Note that most drivers in the current version of the library only allow you to
seek to the next frame (as in the above example). To rewind the file, you may
have to reopen it.

The following iterator class lets you to use the for-statement to loop over the
sequence:

A sequence iterator class
^^^^^^^^^^^^^^^^^^^^^^^^^

::

    class ImageSequence:
        def __init__(self, im):
            self.im = im
        def __getitem__(self, ix):
            try:
                if ix:
                    self.im.seek(ix)
                return self.im
            except EOFError:
                raise IndexError # end of sequence

    for frame in ImageSequence(im):
        # ...do something to frame...


Postscript printing
-------------------

The Python Imaging Library includes functions to print images, text and
graphics on Postscript printers. Here’s a simple example:

Drawing Postscript
^^^^^^^^^^^^^^^^^^

::

    from PIL import Image
    from PIL import PSDraw

    im = Image.open("lena.ppm")
    title = "lena"
    box = (1*72, 2*72, 7*72, 10*72) # in points

    ps = PSDraw.PSDraw() # default is sys.stdout
    ps.begin_document(title)

    # draw the image (75 dpi)
    ps.image(box, im, 75)
    ps.rectangle(box)

    # draw centered title
    ps.setfont("HelveticaNarrow-Bold", 36)
    w, h, b = ps.textsize(title)
    ps.text((4*72-w/2, 1*72-h), title)

    ps.end_document()

More on reading images
----------------------

As described earlier, the :py:func:`~PIL.Image.open` function of the
:py:mod:`~PIL.Image` module is used to open an image file. In most cases, you
simply pass it the filename as an argument::

    im = Image.open("lena.ppm")

If everything goes well, the result is an :py:class:`PIL.Image.Image` object.
Otherwise, an :exc:`IOError` exception is raised.

You can use a file-like object instead of the filename. The object must
implement :py:meth:`~file.read`, :py:meth:`~file.seek` and
:py:meth:`~file.tell` methods, and be opened in binary mode.

Reading from an open file
^^^^^^^^^^^^^^^^^^^^^^^^^

::

    fp = open("lena.ppm", "rb")
    im = Image.open(fp)

To read an image from string data, use the :py:class:`~StringIO.StringIO`
class:

Reading from a string
^^^^^^^^^^^^^^^^^^^^^

::

    import StringIO

    im = Image.open(StringIO.StringIO(buffer))

Note that the library rewinds the file (using ``seek(0)``) before reading the
image header. In addition, seek will also be used when the image data is read
(by the load method). If the image file is embedded in a larger file, such as a
tar file, you can use the :py:class:`~PIL.ContainerIO` or
:py:class:`~PIL.TarIO` modules to access it.

Reading from a tar archive
^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    from PIL import TarIO

    fp = TarIO.TarIO("Imaging.tar", "Imaging/test/lena.ppm")
    im = Image.open(fp)

Controlling the decoder
-----------------------

Some decoders allow you to manipulate the image while reading it from a file.
This can often be used to speed up decoding when creating thumbnails (when
speed is usually more important than quality) and printing to a monochrome
laser printer (when only a greyscale version of the image is needed).

The :py:meth:`~PIL.Image.Image.draft` method manipulates an opened but not yet
loaded image so it as closely as possible matches the given mode and size. This
is done by reconfiguring the image decoder.

Reading in draft mode
^^^^^^^^^^^^^^^^^^^^^

::

    from __future__ import print_function
    im = Image.open(file)
    print("original =", im.mode, im.size)

    im.draft("L", (100, 100))
    print("draft =", im.mode, im.size)

This prints something like::

    original = RGB (512, 512)
    draft = L (128, 128)

Note that the resulting image may not exactly match the requested mode and
size. To make sure that the image is not larger than the given size, use the
thumbnail method instead.

快速入门
========

.. note:: 译者注：编程名词和专业名词尽量不翻译，比如bands, modes等请参考 :doc:`概念 <http://pillow-cn.readthedocs.org/zh_CN/latest/handbook/concepts.html>` 。

使用 Image 类
---------------------

PIL最重要的类是
:py:class:`~PIL.Image.Image` class, 你可以通过多种方法创建这个类的实例；你可以从文件加载图像，或者处理其他图像, 或者从 scratch 创建。

要从文件加载图像，使用 :py:func:`~PIL.Image.open` 函数，
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

只要你有了 :py:class:`~PIL.Image.Image` 类的实例，你就可以通过类的方法处理图像。比如，下列方法可以显示图像::

    >>> im.show()

.. note::

    标准的 :py:meth:`~PIL.Image.Image.show` 效率并不高，它需要保存图像到临时文件然后通过
    :command:`xv` 显示图像。你需要先安装 :command:`xv` ，显示图像有助于调试和测试。

下面的部分提供了这个库其他函数的概览。

读写图像
--------------------------

PIL 模块支持大量图片格式。使用在
:py:mod:`~PIL.Image` 模块的 :py:func:`~PIL.Image.open` 函数从磁盘读取文件。你不需要知道文件格式就能打开它，这个库能够根据文件内容自动确定文件格式。

要保存文件，使用
:py:class:`~PIL.Image.Image` 类的 :py:meth:`~PIL.Image.Image.save` 方法。保存文件的时候文件名变得重要了。除非你指定格式，否则这个库将会以文件名的扩展名作为格式保存。


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

:py:meth:`~PIL.Image.Image.save` 方法的第二个参数可以指定文件格式，如果你使用非标准的扩展名你必须这样做：

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

很重要的一点是这个库不会直接解码或者加载图像栅格数据。当你打开一个文件，只会读取文件头信息用来确定格式，颜色模式，大小等等，文件的剩余部分不会主动处理。这意味着打开一个图像文件的操作十分快速，跟图片大小和压缩方式无关。下面是一个简单的脚本用来快速验证大量图片。


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

:py:class:`~PIL.Image.Image` 类包含的方法允许你操作图像部分选区。使用:py:meth:`~PIL.Image.Image.crop` 方法获取图像的一个子矩形选区。

从图像中复制出一个矩形选区
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    box = (100, 100, 400, 400)
    region = im.crop(box)

矩形选区有一个4元元组定义，分别表示左、上、右、下的坐标。这个库以左上角为坐标原点，单位是px，所以上诉代码复制了一个 300x300 pixels 的矩形选区。这个选区现在可以被处理并且粘贴到原图。

处理复制的矩形选区并粘贴到原图
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    region = region.transpose(Image.ROTATE_180)
    im.paste(region, box)

当你粘贴矩形选区的时候必须保证尺寸一致。此外，矩形选区不能在图像外。然而你不必保证矩形选区和原图的颜色模式一致，因为矩形选区会被自动转换颜色（参看下面的 :ref:`color-transforms`  部分），下面是一个例子：

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

分离和合并颜色通道
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

多帧图像序列
---------------

The Python Imaging Library contains some basic support for image sequences
(also called animation formats). Supported sequence formats include FLI/FLC,
GIF, and a few experimental formats. TIFF files can also contain more than one
frame.

When you open a sequence file, PIL automatically loads the first frame in the
sequence. You can use the seek and tell methods to move between different
frames:

读取序列
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

序列迭代 class
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


Postscript 打印
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

更多关于读取图像
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

从文件读取
^^^^^^^^^^^^^^^^^^^^^^^^^

::

    fp = open("lena.ppm", "rb")
    im = Image.open(fp)

To read an image from string data, use the :py:class:`~StringIO.StringIO`
class:

从 string 读取
^^^^^^^^^^^^^^^^^^^^^

::

    import StringIO

    im = Image.open(StringIO.StringIO(buffer))

Note that the library rewinds the file (using ``seek(0)``) before reading the
image header. In addition, seek will also be used when the image data is read
(by the load method). If the image file is embedded in a larger file, such as a
tar file, you can use the :py:class:`~PIL.ContainerIO` or
:py:class:`~PIL.TarIO` modules to access it.

从压缩包读取
^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    from PIL import TarIO

    fp = TarIO.TarIO("Imaging.tar", "Imaging/test/lena.ppm")
    im = Image.open(fp)

控制解码器
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

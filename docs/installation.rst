安装
============

.. warning:: Pillow >= 2.1.0 不支持 "import _imaging"。请使用 "from PIL.Image import core as _imaging" 代替。

.. warning:: Pillow >= 1.0 不支持 "import Image". 请使用 "from PIL import Image" 代替。

.. warning:: PIL 和 Pillow 现在不能共存。
    如果你想使用 Pillow, 请首先移除 PIL 。

.. note:: Pillow >= 2.0.0 支持 Python 版本 2.6, 2.7, 3.2, 3.3, 3.4

.. note:: Pillow < 2.0.0 支持 Python 版本 2.4, 2.5, 2.6, 2.7.

简单安装
-------------------

.. note::

    下面的指南将会安装支持大多数格式的 Pillow。
    查看 :ref:`external-libraries` 了解安装外部库的信息。这个页面包含了特定平台的安装。

你可以使用 :command:`pip` 命令安装Pillow：

    $ pip install Pillow

或者 :command:`easy_install` (for installing `Python Eggs
<http://peak.telecommunity.com/DevCenter/PythonEggs>`_, as :command:`pip` does
not support them)::

    $ easy_install Pillow

或者从 PyPI 下载 `compressed archive from PyPI`_ ，解压，进入目录运行::

    $ python setup.py install

.. _compressed archive from PyPI: https://pypi.python.org/pypi/Pillow

.. _external-libraries:

外部库
------------------

Pillow 的许多功能需要外部库的支持:

* **libjpeg** 提供 JPEG 相关功能

  * Pillow 已经在这些版本测试过：versions **6b**, **8**, and **9**

* **zlib** 提供压缩 PNG 功能

* **libtiff** 提供 group4 tiff 功能

  * Pillow 已经在这些版本测试过：versions **3.x** and **4.0**

* **libfreetype** 提供类型相关服务

* **littlecms** provides color management

  * Pillow  2.2.1 以及更低版本使用 liblcms1, Pillow 2.3.0 以及更高版本使用 liblcms2. 在这些版本测试通过： **1.19** and **2.2**

* **libwebp** 提供 webp 相关功能

  * Pillow已经在这些版本测试过： version **0.1.3** 不能读取透明webp图像， Versions **0.3.0** and **0.4.0** 支持透明。

* **tcl/tk** 提供 tkinter bitmap and photo images 的支持

* **openjpeg** 提供 JPEG 2000 功能 

  * Pillow 在这些版本测试通过： **2.0.0**

如果机器上已经使用标准方法在标准目录安装了这些库 (e.g. :file:`/usr` or :file:`/usr/local`), 那么不需要进行多余的配置了。如果它们安装在非标准目录，你可能需要配置 setuptools 以使用这些目录。 (i.e. by editing
:file:`setup.py` and/or :file:`setup.cfg`). 当你安装好依赖包之后, 运行::

    $ pip install Pillow

Linux 下安装
------------------

.. note::

    Fedora, Debian/Ubuntu, and ArchLinux 已经包含了 Pillow。

.. note::

    你 **不需要** 安装所有的外部库如果你只想让 Pillow 基本能运行。

**我们不为 Linux 提供二进制文件** 如果你不是从源码构建的 Python , 那么先确定你安装了 Python 开发环境，在 Debian
或 Ubuntu::

    $ sudo apt-get install python-dev python-setuptools

或者使用 Python 3::

    $ sudo apt-get install python3-dev python3-setuptools

在 **Fedora** , 命令是::
    
    $ sudo yum install python-devel

在 **Ubuntu 10.04 LTS** 安装依赖包::

    $ sudo apt-get install libtiff4-dev libjpeg62-dev zlib1g-dev \
        libfreetype6-dev tcl8.5-dev tk8.5-dev python-tk

在 **Ubuntu 12.04 LTS** 或者 **Raspian Wheezy
7.0** 安装依赖包::

    $ sudo apt-get install libtiff4-dev libjpeg8-dev zlib1g-dev \
        libfreetype6-dev liblcms2-dev libwebp-dev tcl8.5-dev tk8.5-dev python-tk

在 **Fedora 20** 安装依赖包::

    $ sudo yum install libtiff-devel libjpeg-devel libzip-devel freetype-devel \
        lcms2-devel libwebp-devel tcl-devel tk-devel


Mac OS X 下安装
---------------------

.. note::

    你 **不需要** 安装所有的外部库如果你只想让 Pillow 基本能运行。

**我们不为 OS X 提供二进制文件**，所以你需要先安装 Xcode 才能安装 Pillow 。 (XCode 4.2 on 10.6 可以和官方 Python 二进制版本使用。或者使用任意版本 XCode 自己编译 Python。)

安装依赖包最简单的方法是使用 `Homebrew
<http://mxcl.github.com/homebrew/>`_。安装 Homebrew 之后，运行::

    $ brew install libtiff libjpeg webp little-cms2

如果你已经构建了Python，可以运行::

    $ pip install Pillow

Windows 下安装
--------------------

我们在 PyPI 提供 Windows 二进制文件，使用 `Python Wheels
<http://wheel.readthedocs.org/en/latest/index.html>`_:

Python Eggs
^^^^^^^^^^^

.. note::

    :command:`pip` 不支持 Python Eggs; 请使用 :command:`easy_install`
    代替。

::

    $ easy_install Pillow

Python Wheels
^^^^^^^^^^^^^

.. note:: 实验性. 需要 setuptools >=0.8 以及 pip >=1.4.1

::

    $ pip install --use-wheel Pillow

如果上面都不行，尝试安装指定版本：

::

    $ pip install --use-wheel Pillow==2.3.0


平台支持
----------------

下面是平台支持情况，一般来说，我们致力于支持大多数平台。

.. note::

    如果你在某平台测试过，编辑这个文件然后在 Github 提交 request 。

+----------------------------------+-------------+------------------------------+------------------------------+-----------------------+ 
|**Operating system**              |**Supported**|**Tested Python versions**    |**Tested Pillow versions**    |**Tested processors**  |
+----------------------------------+-------------+------------------------------+------------------------------+-----------------------+
| Mac OS X 10.8 Mountain Lion      |Yes          | 2.6,2.7,3.2,3.3              |                              |x86-64                 |
+----------------------------------+-------------+------------------------------+------------------------------+-----------------------+
| Mac OS X 10.7 Lion               |Yes          | 2.6,2.7,3.2,3.3              | 2.2.0                        |x86-64                 |
+----------------------------------+-------------+------------------------------+------------------------------+-----------------------+
| Redhat Linux 6                   |Yes          | 2.6                          |                              |x86                    |
+----------------------------------+-------------+------------------------------+------------------------------+-----------------------+
| CentOS 6.3                       |Yes          | 2.7,3.3                      |                              |x86                    |
+----------------------------------+-------------+------------------------------+------------------------------+-----------------------+
| Fedora 20                        |Yes          | 2.7,3.3                      | 2.3.0                        |x86-64                 |
+----------------------------------+-------------+------------------------------+------------------------------+-----------------------+
| Ubuntu Linux 10.04 LTS           |Yes          | 2.6                          | 2.3.0                        |x86,x86-64             |
+----------------------------------+-------------+------------------------------+------------------------------+-----------------------+
| Ubuntu Linux 12.04 LTS           |Yes          | 2.6,2.7,3.2,3.3,PyPy2.1      | 2.3.0                        |x86,x86-64             |
|                                  |             |                              |                              |                       |
|                                  |             | 2.7,3.2                      | 2.3.0                        |ppc                    |
+----------------------------------+-------------+------------------------------+------------------------------+-----------------------+
| Ubuntu Linux 13.10               |Yes          | 2.7,3.2,3.3                  | 2.3.0                        |x86                    |
+----------------------------------+-------------+------------------------------+------------------------------+-----------------------+
| Raspian Wheezy                   |Yes          | 2.7,3.2                      | 2.3.0                        |arm                    |
+----------------------------------+-------------+------------------------------+------------------------------+-----------------------+
| Gentoo Linux                     |Yes          | 2.7,3.2                      | 2.1.0                        |x86-64                 |
+----------------------------------+-------------+------------------------------+------------------------------+-----------------------+
| Windows 7 Pro                    |Yes          | 2.7,3.2,3.3                  | 2.2.1                        |x86-64                 |
+----------------------------------+-------------+------------------------------+------------------------------+-----------------------+
| Windows Server 2008 R2 Enterprise|Yes          | 3.3                          |                              |x86-64                 |
+----------------------------------+-------------+------------------------------+------------------------------+-----------------------+
| Windows 8 Pro                    |Yes          | 2.6,2.7,3.2,3.3,3.4a3        | 2.2.0                        |x86,x86-64             |
+----------------------------------+-------------+------------------------------+------------------------------+-----------------------+
| Windows 8.1 Pro                  |Yes          | 2.6,2.7,3.2,3.3,3.4          | 2.3.0, 2.4.0                 |x86,x86-64             |
+----------------------------------+-------------+------------------------------+------------------------------+-----------------------+


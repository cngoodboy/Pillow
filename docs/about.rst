关于 Pillow
============

愿景
-----

分支作者的愿景是通过以下途径活跃 PIL 的开发：

- 在 `Travis CI`_ 持续测试 ；
- 在 `GitHub`_ 公开开发活动；
- 定期发布到 `Python Package Index`_ ；
- 在 `Image-SIG`_ 争取社区参与与贡献 。

.. _Travis CI: https://travis-ci.org/python-imaging/Pillow
.. _GitHub: https://github.com/python-imaging/Pillow
.. _Python Package Index: https://pypi.python.org/pypi/Pillow
.. _Image-SIG: http://mail.python.org/mailman/listinfo/image-sig

许可证
-------

和PIL一样，Pillow 使用 MIT-like `PIL Software License <http://www.pythonware.com/products/pil/license.htm>`_ ::

    Software License

    The Python Imaging Library (PIL) is

        Copyright © 1997-2011 by Secret Labs AB
        Copyright © 1995-2011 by Fredrik Lundh

    By obtaining, using, and/or copying this software and/or its associated documentation, you agree that you have read, understood, and will comply with the following terms and conditions:

    Permission to use, copy, modify, and distribute this software and its associated documentation for any purpose and without fee is hereby granted, provided that the above copyright notice appears in all copies, and that both that copyright notice and this permission notice appear in supporting documentation, and that the name of Secret Labs AB or the author not be used in advertising or publicity pertaining to distribution of the software without specific, written prior permission.

    SECRET LABS AB AND THE AUTHOR DISCLAIMS ALL WARRANTIES WITH REGARD TO THIS SOFTWARE, INCLUDING ALL IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS. IN NO EVENT SHALL SECRET LABS AB OR THE AUTHOR BE LIABLE FOR ANY SPECIAL, INDIRECT OR CONSEQUENTIAL DAMAGES OR ANY DAMAGES WHATSOEVER RESULTING FROM LOSS OF USE, DATA OR PROFITS, WHETHER IN AN ACTION OF CONTRACT, NEGLIGENCE OR OTHER TORTIOUS ACTION, ARISING OUT OF OR IN CONNECTION WITH THE USE OR PERFORMANCE OF THIS SOFTWARE.

为什么派生分支
-----------

PIL 不兼容setuptools，请阅读 `this Image-SIG post`_ 获取更多解释。而且 PIL 计划一年更新两次太慢了不能解决大量issues报告。

.. _this Image-SIG post: https://mail.python.org/pipermail/image-sig/2010-August/006480.html

关于 PIL 库
---------------

.. note::

    Pillow 2.0.0 之前，Pillow相对于 PIL 更改很少。Pillow 2.0.0
    开始增加 Python 3 的支持进行了很多 Bug 修正。

距离上次 PIL 发布更新的时间越久，PIL 更新的可能性越小。然而，PIL官方还没有正式宣布PIL死亡，如果你还想支持 PIL 可以先报告给 PIL：
`report issues here first`_, 然后报告给 Pillow：
`open the corresponding Pillow tickets here`_.

.. _report issues here first: https://bitbucket.org/effbot/pil-2009-raclette/issues

.. _open the corresponding Pillow tickets here: https://github.com/python-imaging/Pillow/issues

报告给 Pillow 的时候请带上 PIL 的链接方便我们跟踪。
